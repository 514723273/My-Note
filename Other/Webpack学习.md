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

```js
    module: {
        rules: [
            // 图片处理（官网查询，得知使用 file-loader
            // （import require 的时候（发现不是.js结尾）才来查找规则，而不是直接对所有匹配的文件（如果没有引入就不会打包
            {
                test: /\.png$/,
                use: {
                    loader: 'file-loader',
                }
            }
        ],
    }
```
```
npm install file-loader -D
```
```js
import avatar from './picture.png';

console.log(avatar);        // 58cb58f031bce4cd3de4de1a75d6b3df.png // 输出打包后的图片名称（不含路径

const img = new Image();
img.src = avatar;

const rootEle = document.getElementById('root');

rootEle.append(img);
```

file-loader 的作用可以理解为将源文件中的图片打包（复制）到 dist 目录下，同时返回该图片的名称。

## 2.2 使用 Loader 打包静态资源（图片篇）

```js
{
    test: /\.(png|jpg|gif)$/,
    use: {
        loader: 'url-loader',
        options: {
            name: '[name]_[hash].[ext]',    // 改变打包后的图片名称   // 使用占位符
            outputPath: 'images/',          // 打包到文件夹内
            limit: 2048                     // 和 file-loader 几乎一样，除了整个配置。限制图片大小（理由如下
        }
    }
}
```

url-loader 同样可以处理图片，不同的是，会根据图片生成一个base64的字符串 直接嵌入 js 。（如果，图片大，js 特别大，整个页面显示慢（还不如发 http 请求

## 2.3 使用 Loader 打包静态资源（样式篇）

```js
module: {
    rules: [
        {
            test: /\.png$/,
            use: {
                loader: 'file-loader',
            }
        },
        // !!!
        {
            test: /\.scss$/,
            use: ['style-loader', {
                loader: 'css-loader',
                options: {
                    importLoaders: 2,       // 比如引入的一个 scss 中还引入其他 scss，则要求其再走下面两个 loader
                    modules: true,          // css 模块化（然后好像 @import 之类的失效（import styles from './index.css' 使用 styles.index ；实质就是每个 style 单独生成 hash 替换 {index: "_3EdxTq2iR_Y6Bb_mZCLqx3"}
                }
            }, 'scss-loader', 'postcss-loader'],        // !!! 注意是数组       // {!!!} 是有执行顺序的，rules 从下至上，use 从右至左
        }
    ],
}
```

postcss-loader：添加产商前缀。
scss-loader: 解析 scss 语法，翻译为 css 。
css-loader: 会解析引入的 css 中的引入关系，将其整合为一个 css 。
style-loader: 将 css-loader 整合后的内容，添加至 head `<style>.avatar{width: 150px;}</style>`

字体---file-loader

## 2.4 使用 plugins 让打包更便捷

htmlWebpackPlugin 会在打包结束后，自动生成一个 html 文件，并把打包生成的 js 自动引入到这个 html 文件中。

```js
plugins: [
    new HtmlWebpackPlugin({
        template: 'src/index.html',     // 否则就是空白 html （想来一个 id=root 的 div 都没有
    }), 
    new CleanWebpackPlugin(['dist']),   // 打包前删除 dist 目录
    new webpack.HotModuleReplacementPlugin(),   // 热更新
]
```

## 2.5 SourceMap 的配置

不配置的情况下，如果出错，那报错是根据打包后的 js 提示错误行数。

而我们想知道报错的打包前的行数，可以使用 sourceMap ，这个配置只是使用了映射关系，比如源文件的第一行对应打包后的第96行。

推荐以下两个配置方式：

```js
mode: 'production',
devtool: 'cheap-module-eval-source-map',
```

```js
mode: 'development',
devtool: 'cheap-module-souce-map',
```

## 2.6 使用 WebpackDevServer 提升开发效率

```js
devServer: {
    contentBase: './dist',      // 服务器文件所有文件夹
    open: true,                 // 自动打开浏览器
    port: 8090,
    hot: true,                  // 热更新（需要填写插件
    hotOnly: true               // 即使热更新没有成功开启，也不想浏览器自动刷新
}
// npm run webpack-dev-server
// 放置内存，并不会生成 dist 目录（提升打包速度
// 会自动重新打包，并刷新页面
```

### 2.6.1  Hot Module Replacement 热模块更新

支持 CSS 变更及时显示在页面上，JS 变更需要额外写代码。

```js
import number from './number';

if(module.hot) {
    module.hot.accept('./number', () => {
        document.body.remove(document.getElementById('number'));        // 还得自己手动删除之前函数的执行结果
        number();
    })
}
```

## 2.7 使用 Babel 处理 ES6 语法

[babel 官网 webpack 配置](https://babeljs.io/setup#installation) 只翻译了一部分，比如 map 就没有继续翻译，低版本浏览器会不识别。

[@babel/polyfill](https://babeljs.io/docs/en/babel-polyfill) 补充。