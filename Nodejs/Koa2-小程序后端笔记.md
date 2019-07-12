## 1 JWT 实现

### 1.1 登录逻辑

用户访问登录页面的时候，才会访问的逻辑！

- 参数：邮箱、密码
- 成功返回值：token

前端需要保存该token，访问其他 api 的时候，需要前端传递该 token 验证身份。

```js
// token.js

router.post('/', async (ctx) => {
    const v = await new TokenValidator().validate(ctx)                               // 验证 账号长度 密码可选 类型是否合法
    let token;
    switch (v.get('body.type')) {
        case LoginType.USER_EMAIL:                                                   // 邮箱 密码 登录逻辑
            token = await emailLogin(v.get('body.account'), v.get('body.secret'))    // 验证账号密码是否正确匹配
            break
        case LoginType.USER_MINI_PROGRAM:                                            // 小程序 登录逻辑
            token = await WXManager.codeToToken(v.get('body.account'))
            break
        default:
            throw new global.errs.ParameterException('没有相应的处理函数')
    }
    ctx.body = {    // 返回 token
        token
    }
})

// 验证账号密码是否匹配，成功返回生成的 jwt token ，否则内部抛异常
async function emailLogin(account, secret) {
    const user = await
        User.verifyEmailPassword(account, secret)
    return token = generateToken(user.id, Auth.USER)
}
```

#### 1.1.1 生成 jwt token 逻辑

jwt token 就是一段无意义的随意字符串 但是**特殊的**就是可以携带数据!!!

```js
jwt.sign('希望被携带的参数组成的对象', '密钥单独', '配置的对象，例如过期时间');
```

```js
const generateToken = function(uid, scope){
    const secretKey = global.config.security.secretKey      // 密钥 "abasdf#$cdefg" 自己设置的字符串
    const expiresIn = global.config.security.expiresIn      // 过期时间 60*60*24*30
    const token = jwt.sign({
        uid,
        scope
    },secretKey,{
        expiresIn
    })
    return token
}
```

### 1.2 验证 jwt token & 权限逻辑

```js
// middlewares/auth.js 采用中间件的形式
const basicAuth = require('basic-auth')
const jwt = require('jsonwebtoken')

class Auth {
    constructor(level) {
        this.level = level || 1
        Auth.USER = 8
        Auth.ADMIN = 16
        Auth.SUPER_ADMIN = 32
    }

    get m() {                                           // 注意这个 get 说明 m 是属性，不需要 m()
        return async (ctx, next) => {                   // 返回中间件函数
            
            const userToken = basicAuth(ctx.req)        /* 关键!!! 前端使用 basic-auth 加密发送（postman），后端要对应使用 basicAuth(ctx.req) ，解密取 token */
            let errMsg = 'token不合法'

            if (!userToken || !userToken.name) {        // 判断：token 是否存在
                throw new global.errs.Forbbiden(errMsg)
            }
            try {
                var decode = jwt.verify(userToken.name, /* 关键!!! token 解开编码，取出之前登录保存在该 token 的属性值（uid，scope） */
                    global.config.security.secretKey)
            } catch (error) {
                if (error.name == 'TokenExpiredError'){ // 判断：token 是否过期
                    errMsg = 'token已过期'
                }
                throw new global.errs.Forbbiden(errMsg)
            }

            if(decode.scope < this.level){              // 判断权限 decode.scope 表示当前用户的等级 this.level 表示当前要访问的 api 的等级（我的可以使用 拿到当前的 api 和 解码后的用户权限树做对比，看是否存在该条记录 所以不需要 level
                errMsg = '权限不足'
                throw new global.errs.Forbbiden(errMsg)
            }

            // uid,scope
            ctx.auth = {            // 虽然我觉得 scope 没必要继续传递了
                uid:decode.uid,
                scope:decode.scope
            }

            await next()
        }
    }
}

module.exports = {
    Auth
}
```

写 api 时，需先使用该中间件，验证 token 和权限，判断是否能继续执行。
```js
router.get('/:index/previous', new Auth().m, async (ctx) => {})
```
