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

### 4.4 总结

1. 先 build 客户端文件，入口为 client/app.js（内容正常挂载和渲染组件）
2. build 会输出 dist/app.xx.js index.html（分别因为 babel 编译和 html-webpack-plugin 插件）（index.html 中 `<script src="">`标签 src 向服务器发送请求 /public/app.xx.js）
3. 再 build 服务端文件，入口为 client/server.entry.js（内容仅仅是导出 App 节点）
4. build 会输出 dist/server.entry.js
5. 然后新建 express 服务，设置路由。
6. 用 fs 读取 dist/index.html。
7. 引用 dist/server-entry ，调用 ReactSSR.renderToString 方法，解析出 html 节点文本 `<div data-reactroot="">This is app</div>`
8. 最后采用字符串替换的方法，将节点挂载到 html `template.replace('<app></app>', appString)`

## 5 项目开发时的常用配置

### 5.1 客户端实时更新页面

webpack dev server

通过 webpack 启动一个服务器 方便访问编译后的 html css js 。

且所有文件都会放置在内存中，如果文件发生变化，就会自动重新编译，不需要手动 npm run build 。

有任何代码更改，就会立即刷新页面。

（要把 dist 目录删除，否则会读取 dist 目录下的文件内容。（因为它是保存在内存中的，根本就没有输出和用到该目录！）（相当于还是向服务器请求 /public/app.xxx.js 没有该文件））

主要是使用了 webpack-dev-server 。设置了开发环境的配置。

```js
if(isDev) {
    config.entry = {        // 开发环境下 更改 entry
        app: [              // 所有文件打包到同一个文件
            'react-hot-loader/patch',       // 官方做法
            path.join(__dirname, '../client/app.js'), 
        ]
    }
    config.devServer = {
        host: '0.0.0.0',
        port: '8888',
        contentBase: path.join(__dirname, '../dist'),       // 服务启动的文件夹（编译后的静态文件存放的文件夹
        hot: true,          // hot module replacement
        overlay: {
            errors: true,   // 浏览器只显示错误（不显示 warning）
        },
        publicPath: '/public',              // 因为 script 中的 /public 缘故，所以统一前面加 /public
        historyApiFallback: {               // 404 所有请求都返回该页面
            index: '/public/index.html'
        }
    };
    config.plugins.push(new webpack.HotModuleReplacementPlugin());  // hot module replacement
}
```

hot module replacement

编辑后立即看到效果，无需刷新重新获取数据。（更进一步 刷都不刷新 直接显示最新）

修改四个文件，均已在项目中标记。

### 5.2 服务端实时渲染

原代码中是读取 dist 目录下的 index.html 和 app.xx.js ，但是客户端在开发环境下使用实时渲染之后，不会生成这些文件，而是在内存。

## 6 使用 eslint 和 editorconfig 规范代码

eslint 配合 git ：为了最大程度控制每个人的规范，我们可以在 git commit 代码的时候，使用 git hook 调用 eslint 进行代码规范验证，不规范的代码无法提交到仓库（原本是可以不处理这些错误 提交的）。

editorconfig 不同编辑器对文本的格式会有一定的区别，统一规范。（例如行末）

### 6.1 eslint

1. 新建 .eslintrc 文件：
```js
{
  "parser": "babel-eslint",     // 解析 js 代码
  "extends": "airbnb",
  "env": {
    "browser": true,  // 否则使用 window.xxx 会报错
    "es6": true,
    "node": true,
    "jest": true
  },
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      "defaultParams": true
    }
  },
  "rules": {
    "semi": [0],    // 结尾可以不用分号
    "react/forbid-prop-types": [0],
    "arrow-parens": [0],
    "no-param-reassign": [0],
    "arrow-body-style": [0],
    "react/no-danger": [0],
    "react/jsx-filename-extension": [0],
    "import/no-named-as-default": [0],
    "class-methods-use-this": [0]
  }
}
```

1. 安装 一堆插件（因为 airbnb），在 webpack 中添加规则：

```js
{
    enforce: 'pre',     // 报错后 不执行
    test: /.(js|jsx)$/,
    loader: 'eslint-loader',
    exclude: [
        path.resolve(__dirname, '../node_moudules') // 屏蔽
    ]
}
```

### 6.2 editorconfig

```js
root = true     // 根目录

[*]             // 所有文件
charset = utf-8
indent_style = space    // tab 样式
indent_size = 2
end_of_line = lf        // 结尾方式
insert_final_newline = true         // 末尾添加空行
trim_trailing_whitespace = true     // 删除行末空格
```

### 6.3 precommit

安装插件，使在 git commit 前运行其他命令 precommit。
```
npm i husky -D
```

新建 .gitattributes：
```
# Automatically normalize line endings for all text-based files
# http://git-scm.com/docs/gitattributes#_end_of_line_conversion
* text=auto

# For the following file types, normalize line endings to LF on
# checkin and prevent conversion to CRLF when they are checked out
# (this is required in order to prevent newline related issues like,
# for example, after the build script is run)
.*      text eol=lf
*.css   text eol=lf
*.html  text eol=lf
*.js    text eol=lf
*.json  text eol=lf
*.md    text eol=lf
*.sh    text eol=lf
*.txt   text eol=lf
*.xml   text eol=lf
*.vue   text eol=lf
```

添加命令：
```
"lint": "eslint --ext .js --ext .jsx client/",      // client/ 目录
"precommit": "npm run lint"     // git 钩子
```

## 7 项目基本目录结构

- views：用于存放项目功能模块的页面，需要根据路由配置情况分割子集目录。
- config: 存放一些配置目录，比如第三方类库引用，路由配置等。
- store: 存放项目 store 相关的文件，包括数据获取的封装等。
- components: 用于存放非业务组件，或者在多个业务间都要用到的共用组件。

## 8 如何做前端路由

HTML5 API 中的 history 能够让我们控制 url 跳转之后并不刷新页面，而是交给我们的 JS 代码进行相应操作。在 history api 出现之前，我们可以使用 hash 跳转实现。