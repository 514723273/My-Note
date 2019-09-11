## 1 ES6

### 1.1 ES6 模块化如何使用，开发环境如何打包

- 语法： import export （注意有无 default
- 环境：babel 编译 ES6 语法，模块化可用 webpack 和 rollup
  - rollup 功能单一，可集成，可扩展；webpack功能强大
  - rollup 打包后更小

#### 1.1.1 模块化的基本语法

- export
- export default
- import

#### 1.1.2 开发环境配置

浏览器对 es6 语法支持较差，所以需要编译，将其转化为 es5 或者 es4 的语法。

- babel

#### 1.1.3 关于 JS 众多模块化标准

- 没有模块化
- AMD 成为标准， require.js （也有 CMD
- 前端打包工具，是 nodejs 模块化可以被使用
- ES6 出现，想统一现在所有模块化标准
- nodejs 积极支持，浏览器尚未统一

### 1.2 Class 和 普通构造函数有何区别

- Class 在语法更加贴合面向对象的写法
- Class 实现继承更加易读、易理解
- 更易于写 java 等后端语言的使用
- 本质还是语法糖，使用 prototype

#### 1.2.1 JS 构造函数

```js
function MathHandle(x, y) {
    this.x = x
    this.y = y
}

MathHandle.prototype.add = function () {
    return this.x + this.y
}

var m = new MathHandle(1, 2)
console.log(m.add())
```

#### 1.2.2 Class 基本语法

```js
class MathHandle {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    add() {
        return this.x + this.y;
    }
}
const m = new MathHandle(1, 2);
console.log(m.add())
```

#### 1.2.3 语法糖

```js
class MathHandle {
    // ...
}
var m = new MathHandle(1, 2)
// console.log(m.add())

typeof MathHandle  // 'function'
MathHandle.prototype.constructor === MathHandle  // true
m.__proto__ === MathHandle.prototype  // true
```

### 1.3 Promise 的基本使用和原理

- new Promise 实例，而且要 return
- new Promise 时要传入函数，函数有 resolve reject 两个参数
- 成功时执行 resolve() 失败时执行 reject()

#### 1.3.1 未使用 Promise 的情况

```js
const fs = require('fs')
const path = require('path')

function getContentFile (fileName,callback) {
    const fullFileName = path.resolve(__dirname, 'files', fileName)
    fs.readFile(fullFileName, (err, data) => {
        if (err) {
            console.error(err)
            return
        }

        callback(data.toString())
    })
}

// 回调地狱
getContentFile('a.json', aData => {
    console.log('this is a', aData)
    getContentFile('b.json', bData => {
        console.log('this is b', bData)
        getContentFile('c.json', cData => {
            console.log('this is c', cData)
        })
    })
})
```

#### 1.3.1 使用 Promise 的情况

```js
const fs = require('fs')
const path = require('path')

function getContentFile(fileName) {
    const fullFileName = path.resolve(__dirname, 'files', fileName)
    const promise = new Promise((resolve, reject) => {
        fs.readFile(fullFileName, (err, data) => {
            if (err) {
                reject(err)
                return
            }
    
            resolve(
                JSON.parse(data.toString())
            )
        })
    })
    return promise
}

getContentFile('a.json').then(aData => {
    console.log('this aData', aData)
    return getContentFile(aData.next)
}).then(bData => {
    console.log('this bData', bData)
    return getContentFile(bData.next)
}).then(cData => {
    console.log('this cData', cData)
}).catch(err => {
    console.log(err)
})
```

### 1.4 总结一下 ES6 其他常用功能

- let/const
- 多行字符串/模板变量
- 解构赋值
- 块级作用域
- 函数默认参数
- 箭头函数

## 2 虚拟 DOM

### 2.1 virtual dom 是什么 为什么会存在 virtual dom

什么是虚拟 dom
- 用 JS 模拟 DOM 解构
- DOM 变化的对比，放在 JS 层来做（图灵完备语言：能做到逻辑判断（css html 是标记语言））
- 提高重绘性能

### 2.2 virtual dom 如何实现，核心 API 是什么

### 2.3 介绍一下 diff 算法