# 1 安装 yarn

[yarn 安装](https://yarnpkg.com/zh-Hant/docs/install#windows-stable)

# 2 less 支持

## 2.1 緣由

React 官方脚手架生成的项目不支持 less ，然而恰巧的是， AntD 是以 less 开发的。

虽然可以通过直接引入 `import '~/antd/dist/antd.css'` ，但是这样不能后期规范主题（替换），比较死板，不能支持 less 文件。所以需要暴露配置和使用 less 文件。

## 2.2 步驟

1. 使用 `yarn eject` 命令，执行完毕后，会发现 package.json 中增加了很多新依赖和其他文件。

2. 安装 loader
```
yarn add less-loader css-loader style-loader less --save-dev
``` 
3. 在 config 文件夹中添加了暴露出来的 webpack 配置，可以在其中修改配置，使其能加载 Less 文件。

webpack module 下面的 rules 添加一个配置（找到如下位置）：

```js
resolveLoader: {
      plugins: [
        // Also related to Plug'n'Play, but this time it tells Webpack to load its loaders
        // from the current package.
        PnpWebpackPlugin.moduleLoader(module),
      ],
    },
    module: {
      strictExportPresence: true,
      rules: [
        // Disable require.ensure as it's not a standard language feature.
        { parser: { requireEnsure: false } },
    //...
```
在 rules 内添加：
```js
{
    test: /\.less$/,
    use: [
    'style-loader',
    { loader: 'css-loader', options: { importLoaders: 1 } },
    'less-loader'
    ]
},
```

4. 按需加載 antd 组件所需要的样式

下载依赖：
```
yarn add babel-plugin-import
```

在 webpack.config.js 中，找到 plugins 添加配置：
```js
plugins: [
  [
    require.resolve('babel-plugin-named-asset-import'),
    {
      loaderMap: {
        svg: {
          ReactComponent: '@svgr/webpack?-svgo,+ref![path]',
        },
      },
    },
  ],
],
```
添加：
```js
["import", {
  "libraryName": "antd",
  "libraryDirectory": "es",
  "style": "css" // `style: true` 会加载 less 文件
}]
```

就不需要全局引入 antd.css 文件。

# 3 antd 基础后台布局

使用 Row 和 Col 使其按照 3 : 21 分配大小。左半部分只有导航栏，右半部分又分为头、内容、尾。

```js
import React, { Component } from 'react';
import { Row, Col } from 'antd';
import Header from './components/Header';
import Footer from './components/Footer';
import LeftNav from './components/LeftNav';

export default class Admin extends Component {
    render() {
        return (
            <div>
                <Row>
                    <Col span={3}>
                        <LeftNav />
                    </Col>
                    
                    <Col span={21}>
                        <Header />
                        <Row>
                            Content
                        </Row>
                        <Footer />
                    </Col>
                </Row>
            </div>
        )
    }
}
```

# 4 react redux react-redux 使用总结

## 4.1 store 整体配置

### 4.1.1 redux-immutable 中的 combineReducers

使用到 redux-immutable 中的 combineReducers 来组合其他组件的 reducer，使其返回的整个 state 变为 immutable ，统一语法 get 取值。
例如 `state.get('leftNav').get('menuList')` ，而不是 `state.leftNav.get('menuList')`

```js
// src/store/reducer.js

import { combineReducers } from 'redux-immutable';
import { reducer as leftNavReducer } from '../components/LeftNav/store';

export default combineReducers({
    leftNav: leftNavReducer,
});
```

### 4.1.2 redux 中的 createStore

createStore 使用上一节的 reducer ，创建 store 。

compose 只是配合插件使用。

```js
// src/store/index.js
import { createStore, compose } from 'redux';
import reducer from './reducer';

const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;

const enhancer = composeEnhancers(
);

const store = createStore(reducer, enhancer);

export default store;
```

### 4.1.3 react-redux 中的 Provider

是一个组件，需要一个 store 参数---也就是上一节创建的 store ，将 store 提供给所有子组件。

```js
// src/store/Admin.js
import React, { Component } from 'react';
import { Provider } from 'react-redux';       /*!!!*/
import { Row, Col } from 'antd';
import Header from './components/Header';
import Footer from './components/Footer';
import LeftNav from './components/LeftNav';
import GlobalStyle, { AdminWrapper } from './style';
import store from './store';                  /*!!!*/

export default class Admin extends Component {
    render() {
        return (
            <Provider store={store}>          {/*!!!*/}
                <GlobalStyle />
                <AdminWrapper>
                    <Row>
                        <Col span={4} className="left-nav">
                            <LeftNav />
                        </Col>
                        
                        <Col span={20} className="main">
                            <Header />
                            <Row  className="content">
                                Content
                            </Row>
                            <Footer />
                        </Col>
                    </Row>
                </AdminWrapper>
            </Provider>
        )
    }
}
```

## 4.2 分支 store 的配置

### 4.2.1 immutable 中的 fromJS

封装 defaultState ，使其 immutable 。

总 state 引入的 就是这个分支 state 。

```js
// src/components/LeftNav/store/reducer.js
import { fromJS } from 'immutable';

const defaultState = fromJS({
    menuList: [
        {
            title:'首页',
            key:'/home'
        },
        {
            title:'订单管理',
            key:'/order',
            btnList: [
                {
                    title:'订单详情',
                    key:'detail'
                },
                {
                    title:'结束订单',
                    key:'finish'
                }
            ]
        },
        {
            title:'车辆地图',
            key:'/bikeMap'
        },
        {
            title:'图标',
            key:'/charts',
            children: [
                {
                    title:'柱形图',
                    key:'/charts/bar'
                },
                {
                    title:'饼图',
                    key:'/charts/pie'
                },
                {
                    title:'折线图',
                    key:'/charts/line'
                },
            ]
        },
    ],
});

export default (state = defaultState, action) => {
    return state;
};
```

### 4.2.2 react-redux 中的 connect

上面几个小节，已经完成了 store 的整合，这小节是使用。

是 Provider 提供了 store 给这个组件，组件使用映射的属性和方法。

```js
// src/components/LeftNav/index.js
import React, { Component } from 'react';
import { connect } from 'react-redux';

class LeftNav extends Component {
    render() {
        console.log(this.props.menuList);
        return (
            <div>LeftNav</div>
        )
    }
}

const mapStateToProps = (state) => ({
    menuList: state.getIn(['leftNav', 'menuList']).toJS(),
})

const mapDispatchToProps = (dispatch) => ({
})

export default connect(mapStateToProps, mapDispatchToProps)(LeftNav);
```

## 4.3 再总结

1. 配置总 reducer ，稍后为其添加分支 reducer 。
2. 使用reducer，创建整 store 。
3. 通过 Provider 将 store 提供给所有子组件。
4. 配置分支 reducer ，将其分配给总 reducer 。
5. 子组件使用映射，使用 store 底下的属性和方法。

# 5 注意事项

## 5.1 使用calc()计算宽高（vw/vh）

参考 [【CSS笔记】— 使用calc()计算宽高（vw/vh）](https://juejin.im/post/5c18d8e2f265da61407ed721)

vw和vh是什么？

vw、vh、vmin、vmax是一种视窗单位，也是相对单位。它相对的不是父节点或者页面的根节点。而是由视窗（Viewport）大小来决定的，单位 1，代表类似于 1%。

视窗(Viewport)是你的浏览器实际显示内容的区域，换句话说是你的不包括工具栏和按钮的网页浏览器。
具体描述如下：

- vw：视窗宽度的百分比（1vw 代表视窗的宽度为 1%）
- vh：视窗高度的百分比
- vmin：取当前Vw和Vh中较小的那一个值
- vmax：取当前Vw和Vh中较大的那一个值

## 5.2 Flex 布局

参考 [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)