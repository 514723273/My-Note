## 1 架构

### 1.1 工程架构

为了更方便规范地开发。

- 解放生产力
    - 源代码预处理（例如 babel 编译
    - 自动打包，自动更新页面显示
    - 自动处理图片依赖，保证开发和正式环境的统一
- 围绕解决方案搭建环境
    - 不同的前端框架需要不同的运行架构（react jsx
    - 预期可能出现的问题并规避（直接写 css 模块能力差
- 保证项目质量
    - code lint
    - 不同环境排除差异（例如回车
    - git commit 预处理（提交前进行 eslint 检测

### 1.2 项目架构

业务代码如何分层、扩展，如何更好地实现功能。

- 技术选型（选择框架、配套框架
- 数据解决方案（api、数据框架
- 整体代码风格

## 2 web 开发常用网络优化

### 2.1 优化方法

- 合并资源文件，减少HTTP请求
- 压缩资源文件，减小请求大小
- 利用缓存机制，尽可能使用缓存减少请求

## 3 项目目录

- build: 放各种配置文件、脚本文件
- client: 放所有前端项目的文件
- dist：webpack 编译后的结果会输出在这

## 4 React 中如何使用服务端渲染

react-dom 是 React 专门为 web 端开发的渲染工具。

我们可以在客户端使用 react-dom 的 render 方法渲染组件，

而在服务端，react-dom/server 提供将 react 组件渲染成 HTML 的方法。

### 4.1 npm run clear

```
"clear": "rimraf dist",
```

使用 rimraf 包，删除 dist 目录及其所有文件。

### 4.2 npm run build:client

```
"build:client": "webpack --config build/webpack.config.client.js",
```

读取 webpack.config.client.js 中的配置，进行编译整合，输出 index.html app.xxxx.js。

- `new HtmlWebpackPlugin({template: path.join(__dirname, '../client/template.html')})` 插件将使用 template.html 作为模板输出 dist/index.html，用 babel 编译的 js 文件将被引用至该 html 的 body 末尾。
- 编译入口为 app.js ，其中引入 App.jsx 并 `document.getElementById('root')`。也就是说只编译所有的 js 文件。
- 正常输出至 dist 。其中`publicPath: '/public'`这条配置，使 index.html 引用 js 时，添加前缀 /public `<script type="text/javascript" src="/public/app.b198c87a3cc7b895a109.js"></script>`。此时打开显示空白页，因为 js 引用目录不对。这个配置是为服务端渲染静态文件做准备。若要正常显示，删除 /public 即可。（但若是删除，浏览器解析 html 继续请求 localhost:8000/app.xxx.js 此时只会匹配 * 路由，又会执行里面的函数，返回整合后的 index.html 文本，此时会被视作 js 代码，不会继续解析其 src 的内容，无限请求。

### 4.3 npm run build:server

```
"build:server": "webpack --config build/webpack.config.server.js",
```
读取 webpack.config.client.js 中的配置，进行编译整合，输出 server-entry.js。

- 服务端的 webpack 和客户端的 webpack 配置只有几个地方不同。
    - target: 'node',        // 哪个环境中执行
    - 文件入口变为 ../client/server.entry.js
    - libraryTarget: 'commonjs2'      // 模块加载方案
    - filename: 'server-entry.js',    // 输出文件名。服务端没有缓存 没必要加 hash
    - 去掉了可以输出 index.html 的插件 HtmlWebpackPlugin
- server.entry.js 是 服务端 webpack 打包的入口，与 客户端入口文件 app.js 大致一样结构，引入 App 组件，但是是直接导出 export default <App /> 并没有渲染。
- 编写 server/server.js 引入通过 server.entry.js 编译后的 dist/server-entry.js，按照道理导入的是 App 组件相关的对象 （`const serverEntry = require('../dist/server-entry').default;`）
- 通过 `const appString = ReactSSR.renderToString(serverEntry);` 渲染并导出一个节点文本。
- `template.replace('<app></app>', appString)` 将该节点文本挂载到 template 底下。（ template 是通过文件读取的方式载入的文本。`const template = fs.readFileSync(path.join(__dirname, '../dist/index.html'), 'utf8');`）
- res.send 返回该替换后的文本结果。
- 当文本被浏览器按照 html 解析时，发现`<script src=/public/app.xxx.js">`会继续向服务端请求该静态 js 文件。（由于服务端已经设置访问 /public url 的路由处理。 app.use('/public', express.static(path.join(__dirname, '../dist'))); 所以按需返回静态文件。


