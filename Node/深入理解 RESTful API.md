# 深入理解 RESTful API

## [RESTful API 最佳实践](https://developer.github.com/v3/ )

- 请求设计规范
  - URI 使用名词，尽量用复数，如 /users
  - URI 使用嵌套表示关系，如 /users/12/repos/5
  - 使用正确的 HTTP 方法，如 GET/POST/PUT/DELETE
  - 如果是不符合 CRUD 的情况：POST + 动词（参考 [github 例子](https://developer.github.com/v3/repos/#transfer-a-repository)）、?action=XXX、设计成子资源 users/repos/star
- 响应设计规范
  - 查询
  - [分页](https://developer.github.com/v3/#pagination)
  - 字段过滤
  - 状态码
  - [错误处理](https://developer.github.com/v3/#client-errors)
- 安全
  - HTTPS
  - [鉴权](https://developer.github.com/v3/#authentication)
  - [限流](https://developer.github.com/v3/#rate-limiting)
- 开发者友好
  - 文档
  - 超媒体

## Koa 路由

### 如果没有路由会有怎么样的结果？

没有路由，所有的请求都会返回相同的结果。

### 原生实现方式

1. 处理不同的 URL
2. 处理不同的 HTTP 方法
3. 解析 URL 上的参数
4. 使用`if......else......`通过判断`ctx.url`和`ctx.method`来区别路由，实现最简单的路由功能

```js
const Koa = require("koa");
const app = new Koa();

app.use(async (ctx, next) => {
  // 通过 ctx.url，处理不同的 URL
  if (ctx.url === "/") {
    ctx.body = "这是主页";
  } else if (ctx.url === "/users") {
    // 通过 ctx.method，处理不同的 HTTP 方法
    if (ctx.method === "GET") {
      ctx.body = "这是用户列表页";
    } else if (ctx.method === "POST") {
      ctx.body = "创建用户";
    } else {
      ctx.status = 405; // 405 Method Not Allowed. 404 路由不行，405 方法不行。
    }
    // 通过正则，解析 URL 上的参数
  } else if (ctx.url.match(/\/users\/\w+/)) {
    const userId = ctx.url.match(/\/users\/(\w+)/)[1];
    ctx.body = `这是用户 ${userId}`;
  } else {
    ctx.status = 404;
    // 比如乱输入域名，是返回 HTTP ERROR 502 。
    // 返回 404 说明服务器是在线的，只是你输入的 url 有问题。
  }
});

app.listen(3000);
```

### 使用 koa-router 实现路由

```js
const Koa = require("koa");
const Router = require("koa-router");

const app = new Koa();
const router = new Router();
const usersRouter = new Router({ prefix: "/users" }); // 这里的斜线是必须的，不然就是 not found

// 多中间件
const auth = async (ctx, next) => {
  // 模拟鉴权
  // 这个中间件的作用是：最后一个多了 :id 的不行，其他都可以
  if (ctx.url !== "/users") {
    ctx.throw(401);
  }
  await next();
};

router.get("/", (ctx) => {
  ctx.body = "这是主页";
});

usersRouter.get("/", auth, (ctx) => {
  ctx.body = "这是用户列表页";
});
usersRouter.post("/", auth, (ctx) => {
  ctx.body = "创建用户";
});
usersRouter.get("/:id", auth, (ctx) => {
  ctx.body = `这是用户 ${ctx.params.id}`;
});

// 注册路由
app.use(router.routes());
app.use(usersRouter.routes());

app.listen(3000);
```

## 其他

### HTTP options 方法的作用是什么？

- 检测服务器所支持的请求方法
- CORS 中的预检请求

### allowedMethods 的作用是什么？

- 响应 options 方法，告诉它所支持的请求方法（不写下面的代码，返回 404；写了，返回这个 URL 可以响应的请求方法）
- 相应地返回 405（不允许，这个 URL 我只实现了 GET 请求方法，没有写 POST 请求方法）和 501（没实现，Koa 只支持 GET、POST等常见的请求方法，不支持 Link、Lock 等请求方法）

```js
app.use(usersRouter.allowedMethouds());
```

### RESTful API 最佳实践——增删改查应该返回什么响应？

```js
const usersRouter = new Router({ prefix: "/users" });

// 查（列表）
usersRouter.get('/', (ctx) => {
    ctx.body = [
      {
          name: '李雷',
      }, {
          name: '韩梅梅',
      }
    ];
});

// 查
usersRouter.get('/:id', (ctx) => {
   ctx.body = { name: '李雷' };
});

// 增
usersRouter.post('/:id', (ctx) => {
   ctx.body = { name: '马冬梅' };	// 返回添加后的实体
});

// 改
usersRouter.put('/:id', (ctx) => {
   ctx.body = { name: '李雷2' };	// 返回修改后的实体
});

// 删
usersRouter.delete('/:id', (ctx) => {
   ctx.status = 204;	// 表示没有内容，但是成功了
});

```

