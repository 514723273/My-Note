# 1 Webpack 初探

## 1.1 Webpack 是 JS 的翻译器？

原始 HTML 页面，引入 index.js 将所有的内容都放在一个文件内，里面涉及新建 Header、新建 Footer、新建 Content。

可能因为 Header 一部分出现问题，而需要动辄浏览所有 index.js 的代码找错误，所以后来出来了模块化，拆分文件。

index.js 分为 Header Footer Content 三个文件，index.html 分别引入，但是要保证顺序引入，否则报错，而且增加了 HTTP 请求（本来一个，现在变成四个）。

所以使用了 import 语法，确认了文件依赖关系，且只请求一个 JS 脚本。

但是新的问题又出现了，浏览器不识别 import 语法。所以出现了 Webpack，翻译了 index.js ，生成 dist/main.js，index.html 引入，程序正常运行。

## 1.2 什么是模块打包工具？

Webpack 是**模块**打包工具。

Webpack 其实只认识 import 语法，其他高级的 JS 语法一概不认。（只认模块，并没有其他特别的功能（可以 requie 也可以 import 都支持

## 1.3 Webpack的正确安装方式

Webpack-cli 的作用是使我们能够在命令行中正常使用 Webpack 命令。


0. 初始化项目
```
npm init
```

1. 全局安装方式

不推荐，因为不同项目 webpack 版本可能不同。
```
npm install webpack webpack-cli -g

npm uninstall webpack webpack-cli -g
```

1. 项目内安装
```
npm install webpack webpack-cli -D

npx webpack -v  // npx 运行当前目录下的安装包，否则报错     // 也可以在 package.json 中配置 脚本 "bundle": "webpack" 运行 npm run bundle
```
```
// 一些其他 npm 命令
npm info webpack        // 查询 webpack ，里面有版本号
npm install webpack@4.24.0 webpack-cli -D // 下载对应版本 
```

## 1.4 使用Webpack的配置文件

之前都是使用 webpack 的默认配置。

```
npx webpack --config webpackconfig.js       // 默认是使用 webpack.config.js 作为配置文件名，但是也可以修改
```

```js
const path = require('path');

module.exports = {
    mode: 'development',        // 决定打包后的代码是否被压缩
    entry: './src/index.js',
    output: {
        filename: 'bundle.js',
        path: path.resovle(__dirname, 'dist'),
    }
}
```

# 2 Webpack 的核心概念

## 2.1 什么是 loader