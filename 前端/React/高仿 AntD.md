#  高仿 AntD

## 代码规范&文件结构

```bash
npx create-react-app my-app --template typescript
```

[create-react-app 官方文档关于升级编辑器](https://create-react-app.dev/docs/setting-up-your-editor/) 好像 CRA 自带了 ESLint，需要配置（目录上没有关于 ESLint 相关的文件，我有点忘了）

[React 官方文档关于文件结构的介绍](https://coding.imooc.com/class/428.html)：

- Grouping by features or routes: One common way to structure projects is to locate CSS, JS, and tests together inside folders grouped by feature or route.
- ~~Grouping by file type: Another popular way to structure projects is to group similar files together（我觉得不常用）~~

- Avoid too much nesting
- **Don’t overthink it: **不要超过五分钟思考这个问题

```bash
# Grouping by features or routes
common/
  Avatar.js
  Avatar.css
  APIUtils.js
  APIUtils.test.js
feed/
  index.js
  Feed.js
  Feed.css
  FeedStory.js
  FeedStory.test.js
  FeedAPI.js
profile/
  index.js
  Profile.js
  ProfileHeader.js
  ProfileHeader.css
  ProfileAPI.js
```

```bash
# ant-design 的目录结构
ant-design/
	components/
		auto-complete/ # 注意都是中横线
			__tests__/
			demo/
			style/
				index.less
				inndex.tsx # 只是 import?
			index.tsx
	docs/
	scripts/
	site/
	tests/
	typings/
	...
```

## 样式解决方案分析

### 三种方案

- inline CSS
- CSS in JS：CSS 不是可编程语言；
  - Styled Component
- Sass/Less（采用）
  - **“_”文件名开头表示 sass 不编译到 css 文件中（Partials）**；导入的时候（@import）不需要添加“\_”
  - 接上，`npx node-sass styles/_variables.scss var.css`编译出来为空的文件
  - 接上，参考第二部分代码

### 使用 Sass 目录结构

```bash
# 样式文件目录

styles/
	_variables.scss # 各种变量（比如色彩、字体）以及可配置开关
	_mixins.scss # 全局 mixins（任何预处理器的基础特性，使重用各种 CSS 更简单）
	_functions.scss # 全局 functions（与 mixins 的不同是，functions 通过计算返回，而 mixins 没有返回）
	_reboot.scss # normalize CSS
	index.scss
components/
	Button/
		style.scss # 组件单独的样式
```

```scss
// index.scss

// 引入不需要下划线
@import "variables";
@import "reboot";

// button 将所有的组件样式都在这个文件中引入
@import "../components/Button/style";

// menu
@import "../components/Menu/style";

// 只需要在 index.tsx 引入 import "./styles/index.scss" 即可
```

### mixin 使用

在`_mixin.scss`中定义：

```scss
// 感觉相当于定义了一个函数
@mixin button-size($padding-y, $padding-x, $font-size, $border-raduis) {
  padding: $padding-y $padding-x;
  font-size: $font-size;
  border-radius: $border-raduis;
}
```

在`Button/style`中使用：

```scss
.btn {
  @include button-size( $btn-padding-y,  $btn-padding-x,  $btn-font-size,  $border-radius);
    
  &.disabled,
  &[disabled] {
    cursor: not-allowed;
    opacity: $btn-disabled-opacity;
    box-shadow: none;
    > * {
      pointer-events: none; // 不会触发点击事件
    }
  }
}

.btn-lg {
  @include button-size($btn-padding-y-lg, $btn-padding-x-lg, $btn-font-size-lg, $btn-border-radius-lg);
}

.btn-sm {
  @include button-size($btn-padding-y-sm, $btn-padding-x-sm, $btn-font-size-sm, $btn-border-radius-sm);
}
```

等效于：

```scss
.btn {
  padding: $btn-padding-y $btn-padding-x;
  font-size: $btn-font-size;
  border-radius: $border-radius;
}
```

### 添加 [normalize.css](https://github.com/necolas/normalize.css)

与 reset.css 都是的比较：

- reset.css 是将所有的浏览器的样式都抹掉
- normalize.css 保护了有用的浏览器的默认样式，为所有样式提供统一的一般性的样式

## 组件库样式变量分类

- 基础色彩系统
- 字体系统
- 表单
- 按钮
- 边框 & 阴影
- 可配置开关

### 基础色彩系统

- 系统色板 = [基础色板（中国色）](http://zhongguose.com/) + [中性色板（黑白灰）](https://ant.design/docs/spec/colors-cn#%E4%B8%AD%E6%80%A7%E8%89%B2%E6%9D%BF)
- 产品色板 = 品牌色 + 功能色
  - 品牌色：一般两种颜色：主色、副色。比如提到可口可乐，就想到可口可乐；提到百事可乐，就想到蓝红色
  - 功能色：就是要保持用户的基本认知，包括提醒、失败、成功

```scss
// 基础色板
$blue:    #0d6efd !default;
$indigo:  #6610f2 !default;
$purple:  #6f42c1 !default;
$pink:    #d63384 !default;
$red:     #dc3545 !default;
$orange:  #fd7e14 !default;
$yellow:  #fadb14 !default;
$green:   #52c41a !default;
$teal:    #20c997 !default;
$cyan:    #17a2b8 !default;

// 中性色板
$white:    #fff !default;
$gray-100: #f8f9fa !default;
$gray-200: #e9ecef !default;
$gray-300: #dee2e6 !default;
$gray-400: #ced4da !default;
$gray-500: #adb5bd !default;
$gray-600: #6c757d !default;
$gray-700: #495057 !default;
$gray-800: #343a40 !default;
$gray-900: #212529 !default;
$black:    #000 !default;

// 系统色板（从上面取值）
$primary:       $blue !default;
$secondary:     $gray-600 !default; // 中性
$success:       $green !default;
$info:          $cyan !default;
$warning:       $yellow !default;
$danger:        $red !default;
$light:         $gray-100 !default; // 中性
$dark:          $gray-800 !default; // 中
```

### 字体系统

```scss
$font-family-sans-serif:      -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Sans", sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji" !default;
$font-family-monospace:       SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace !default;
$font-family-base:            $font-family-sans-serif !default;

// 字体大小
$font-size-base:              1rem !default; // Assumes the browser default, typically `16px`
$font-size-lg:                $font-size-base * 1.25 !default;
$font-size-sm:                $font-size-base * .875 !default;
$font-size-root:              null !default;

// 字重
$font-weight-lighter:         lighter !default;
$font-weight-light:           300 !default;
$font-weight-normal:          400 !default;
$font-weight-bold:            700 !default;
$font-weight-bolder:          bolder !default;
$font-weight-base:            $font-weight-normal !default;

// 行高
$line-height-base:            1.5 !default;
$line-height-lg:              2 !default;
$line-height-sm:              1.25 !default;

// 标题大小
$h1-font-size:                $font-size-base * 2.5 !default;
$h2-font-size:                $font-size-base * 2 !default;
$h3-font-size:                $font-size-base * 1.75 !default;
$h4-font-size:                $font-size-base * 1.5 !default;
$h5-font-size:                $font-size-base * 1.25 !default;
$h6-font-size:                $font-size-base !default;

// 链接
$link-color:                              $primary !default;
$link-decoration:                         none !default;
$link-hover-color:                        darken($link-color, 15%) !default;
$link-hover-decoration:                   underline !default;

// body
$body-bg:                   $white !default;
$body-color:                $gray-900 !default;
$body-text-align:           null !default;
```

## Button 编码

### 目录结构

```bash
Button
    _style.scss
    button.stories.tsx
    button.test.tsx
    button.tsx
    index.tsx
```

### button.tsx

```react
import React, { FC, ButtonHTMLAttributes, AnchorHTMLAttributes } from 'react'
import classNames from 'classnames'

export type ButtonSize = 'lg' | 'sm'
export type ButtonType = 'primary' | 'default' | 'danger' | 'link'

interface BaseButtonProps {
  className?: string;
  /**设置 Button 的禁用 */
  disabled?: boolean;
  /**设置 Button 的尺寸 */
  size?: ButtonSize;
  /**设置 Button 的类型 */
  btnType?: ButtonType;
  children: React.ReactNode;
  href?: string;
}

// 兼顾 button 原生支持的属性
type NativeButtonProps = BaseButtonProps & ButtonHTMLAttributes<HTMLElement>
type AnchorButtonProps = BaseButtonProps & AnchorHTMLAttributes<HTMLElement>
export type ButtonProps = Partial<NativeButtonProps & AnchorButtonProps> // 将所有属性变为可选的（因为可能存在以下情况：在 button 是必须的，在 anchor 选不到这个属性）
/**
 * 页面中最常用的的按钮元素，适合于完成特定的交互
 * ### 引用方法
 * 
 * ~~~js
 * import { Button } from 'vikingship'
 * ~~~
 */
export const Button: FC<ButtonProps> = (props) => {
  const { 
    btnType,
    className,
    disabled,
    size,
    children,
    href,
    ...restProps // 取出剩余的原生
  } = props
  // btn, btn-lg, btn-primary
  const classes = classNames('btn', className, {
    [`btn-${btnType}`]: btnType,
    [`btn-${size}`]: size,
    'disabled': (btnType === 'link') && disabled
  })
  if (btnType === 'link' && href ) {
    return (
      <a
        className={classes}
        href={href}
        {...restProps}
      >
        {children}
      </a>
    )
  } else {
    return (
      <button
        className={classes}
        disabled={disabled}
        {...restProps}
      >
        {children}
      </button>
    )
  }
}

Button.defaultProps = {
  disabled: false,
  btnType: 'default'
}

export default Button;
```

### button.test.tsx

原生 jest 主要是针对**通用类型**的测试，比如 boolean，number，string，array 等。jest-dom 主要是在此基础之上，添加了 dom 类型的断言。

在`src/setupTests.ts`中添加引用`import '@testing-library/jest-dom/extend-expect';`。之后在写测试的时候就会添加上一些新的方法。

```tsx
import React from 'react'
import { render, fireEvent } from '@testing-library/react'
import Button, { ButtonProps } from './button'
const defaultProps = {
  onClick: jest.fn()
}

const testProps: ButtonProps = {
  btnType: 'primary',
  size: 'lg',
  className: 'klass'
}

const disabledProps: ButtonProps = {
  disabled: true,
  onClick: jest.fn(),
}

describe('test Button component', () => {
  // 第一个测试用例，it = test
  it('should render the correct default button', () => {
    const wrapper = render(<Button {...defaultProps}>Nice</Button>)
    const element = wrapper.getByText('Nice') as HTMLButtonElement
    expect(element).toBeInTheDocument()
    expect(element.tagName).toEqual('BUTTON')
    expect(element).toHaveClass('btn btn-default')
    expect(element.disabled).toBeFalsy()
    fireEvent.click(element)
    expect(defaultProps.onClick).toHaveBeenCalled()
  })
  // 第二个测试用例
  it('should render disabled button when disabled set to true', () => {
    const wrapper = render(<Button {...disabledProps}>Nice</Button>)
    const element = wrapper.getByText('Nice') as HTMLButtonElement
    expect(element).toBeInTheDocument()
    expect(element.disabled).toBeTruthy()
    fireEvent.click(element)
    expect(disabledProps.onClick).not.toHaveBeenCalled()
  })
})
```

## Storybook

## 模块打包

### JavaScript 模块发展历史

- 第一阶段：全局变量 + 命名空间
- 第二阶段：common.js + amd
- 第三阶段：es6

```js
// 第一阶段：全局变量 + 命名空间
var myRevealingModule = (function () {
    var prvateVar = "Ben Cherry",
        publicVar = "Hey there!";
    
    function publicSetName(strName) {
        privateVar = strName;
    }
    
    return {
        setName: publicSetName,
        greeting: publicVar,
    }
})();

// 使用例子
myRevealingModule.setName("Paul kinlan");

// 缺点：会污染全局作用域，也不安全（存在被覆盖的风险）；手动管理依赖依赖，要控制引入的顺序；上线前需要手动合并所有的模块
```

```js
// 第二阶段：common.js + amd

// common.js
const bar = require('./bar');

module.exports = function() {
    // ...
}

// 缺点：只适用于服务器端，并不能在浏览器中使用

// AMD
define(function (require) {
    // 通过相对路径获得依赖模块
    const bar = require('./bar');
    // 模块产出
    return function() {
        // ...
    }
})

// 支持打包工具分析依赖，并且合并依赖
// AMD 也没法在浏览器中直接运行，需要使用 AMD 加载工具来运行（比如 require.js）
```

```js
// 第三阶段：es6

// 通过相对路径获得依赖模块
import bar from './bar';

export default function() {
    // ...
}

// 同样不太被浏览器支持
```

### UMD 介绍 & 选择 ES6

**最终要选择什么样的打包后格式供用户使用？**如果打包最后生成 common.js 或者 es6 都是不能被用户直接使用，需要用户使用特殊的 module bundler 来使用，比如 webpack，rollup。还有 AMD 已经恨少人使用，同样需要 module bundler。

UMD（Universal Module Definition）是一个通用的 JavaScript 格式，兼容 common.js 和 amd，而且可以在浏览器直接使用。该格式主要是通过判断代码中是否存在`define`或者`exports`，来区分是 AMD 还是 common.js 或是浏览器。

例如在 [antd 官网的浏览器引入部分](https://ant.design/docs/react/introduce-cn#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%BC%95%E5%85%A5)，就介绍到在浏览器中可以使用`script`和`link`标签直接引入文件。但是这样无法按需加载，而且难以获得底层依赖模块的 bug 快速修复支持，因此强烈不推荐。

**为什么 ES 模块比 CommonJS 更好？**因为 ES 模块是官方标准，也是 JavaScript 语言明确的发展方向，而 CommonJS 模块是一种特殊的传统格式，在 ES 模块被提出之前作为暂时的解决方案。ES 模块允许进行静态分析，从而实现像 tree-shaking 的优化。

现在很多工具都是通过 Webpack 来构建，所以最终选择将 TypeScript 代码打包成 ES 模块。更具体的**打包差别**可以参考 [tsconfig module 的配置](https://www.typescriptlang.org/tsconfig#module)。

### 组件库模块入口文件

```tsx
// src/index.tsx
// 把之前的代码都删掉（CRA 默认生成的，主要是显示页面的），然后导出所有组件

export { default as Button } from './components/Button'
export { default as Menu } from './components/Menu'
```

```tsx
// src/components/Menu/jindex.tsx

import { FC } from 'react'
import Menu, { MenuProps } from './menu'
import SubMenu, { SubMenuProps } from './subMenu'
import MenuItem, { MenuItemProps } from './menuItem'

export type IMenuComponent = FC<MenuProps> & {
  Item: FC<MenuItemProps>,
  SubMenu: FC<SubMenuProps>
}
// 因为直接 Menu.Item = MenuItem 在 TS 中是错误的（类型问题），所以需要联合类型和强转，再对其进行赋值
const TransMenu = Menu as IMenuComponent

TransMenu.Item = MenuItem
TransMenu.SubMenu = SubMenu

export default TransMenu
```

### tsconfig 编写 & TS 文件编译

```json
// tsconfig.build.json
{
  "compilerOptions": {
    "outDir": "dist",
    "module": "esnext",  // 选项有 UMD, AMD, System, ESNext
    "target": "es5",     // 进一步选择编译生成的 ES 版本
    "declaration": true, // 生成 .d.ts，方便其它用户编写代码（有提示）
    "jsx": "react",      // 使用 createElement 来代替语法糖
    "moduleResolution":"Node",
    "allowSyntheticDefaultImports": true,
  },
  "include": [
    "src"
  ],
  "exclude": [
    "src/**/*.test.tsx",
    "src/**/*.stories.tsx",
    "src/setupTests.ts",
  ]
}
```

使用`tsc -p tsconfig.build.json`进行编译，会在`build`文件下按照`src`下的文件顺序生成编译后的`.js`文件和`.d.ts`文件。

### SASS 文件单独编译

使用`node-sass ./src/styles/index.scss ./build/index.css`命令进行编译，其中`./src/styles/index.scss`为整个项目的 sass 入口。在实际使用中，可以单独引入`./build/index.css`即可。

### 使用 npm link 本地测试

完成 TS 文件编译和 SASS 编译之后，进行本地测试。使用 npm link，无需发布，直接本地测试修改后的代码。

首先在 xxx 中的`package.json`中添加入口文件说明

```json
{
    "version": "0.1.0",
    "main": "build/index.js",
    "module": "build/index.js",
    "types": "build/index.d.ts",
}
```

创建软连接的过程：

1. 在被 link 的文件夹 xxx 中运行`npm link`，命令行提示`/Users/.nvm/version/node/v12.5.0/lib/node_modules/xxx -> /Users/Public/xxx`，即创建了一个软连接，在全局的 node_modules 链接到该文件夹。
2. 在测试的文件夹 xxxTest 中运行`npm link xxx`，命令行提示`/Users/Public/xxxTest/node_modules/xxx -> /Users/.nvm/version/node/v12.5.0/lib/node_modules/xxx -> /Users/Public/xxx`，即本地 node_modules 下创建一个软连接，通过全局软连接进行中转，到本地 xxx。
3. 在 xxx 的`package.json`中**手工**添加 xxx 依赖 `"xxx": "0.1.0"`

在使用过程中如果有报错（两个 React 版本引起的），则在 xxx 文件夹下使用命令`npm link ../xxxTest/node_modules/react`。具体参考 [Duplicate React](https://reactjs.org/warnings/invalid-hook-call-warning.html#duplicate-react)。

<img src="C:/Users/KiyonamiYu/Downloads/未命名 (2).png" alt="未命名 (2)" style="zoom:50%;" />

## 发布到 NPM

### 注册 NPM 账号

```bash
npm whoami # 查看自己是否已经登录了
npm config ls # 查看当前 npm 的信息（不能使用淘宝的镜像，否则无法注册）
npm adduser # 注册或登录
```

### [版本包规则](https://semver.org/lang/zh-CN/)

版本格式：主版本号.次版本号.修订号，版本号递增规则如下：

1. 主版本号：当你做了不兼容的 API 修改，
2. 次版本号：当你做了向下兼容的功能性新增，
3. 修订号：当你做了向下兼容的问题修正。

先行版本号及版本编译元数据可以加到“主版本号.次版本号.修订号”的后面，作为延伸。

### 第一次发布流程

1. 在`package.json`中添加必要的字段，具体见下例
2. 在`package.json`中的`script`下新增钩子命令 `"prePublish": "npm run build"`。该命令会在 publish 前自动执行打包构建
3. 整理依赖，即只用于开发的都移入到`"devDependencies"`
4. 使用`peerDependencies`（理由见下例），同时将`react`和`react-dom`也移入`"devDependencies"`
5. 命令行输入`npm run publish`即可发布成功

```json
// package.json 文件

"name": "xxx",
"version": "0.1.0",
"description": "React components library",

"private": false,
"license": "MIT", // 开源协议
"keywords": [     // 关键词
    "Component",
    "UI",
],
"homepage": "http://vikingship.xyz", // 主页相关
"repository": {  // 仓库地址相关
    "type": "git",
    "url": "https://github.com/vikingmute/vikingship"
},
"files": [ // ！！！需要发布的文件（没有配置则根据 gitignore 发布）
    "dist"
],

"peerDependencies": {
    "react": ">=16.8.0",
    "react-dom": ">=16.8.0"
},
// 本项目依赖`react`
// 为了避免出现`Duplicate React`的错误，使用 "peerDependencies" 要求引入的项目必须**先**下载核心依赖库`react`和`react-dom`
// 如果直接`npm install xxx`，则会弹出警告，要求其先手动安装
```

### 发布前测试 & 代码提交前测试

关于 test 部分参考 [create-react-app 中 running test 说明](https://create-react-app.dev/docs/running-tests/#continuous-integration)。

```json
  "scripts": {
    "lint": "eslint --ext js,ts,tsx src --max-warnings 5",
    "test": "react-scripts test", // 这个只在开发的时候用，不会返回最终的结果，处于一个 watch 模式下，随意需要做成和 lint 一样返回结果
    "test:nowatch": "cross-env CI=true react-scripts test", // cross-env 是一个依赖，跨平台统一命令
	// 代码发布前，运行单元测试、lint 检查、打包编译
    "prepublishOnly": "npm run test:nowatch && npm run lint && npm run build"
  },

  "eslintConfig": {
    "extends": "react-app"
  },

  "husky": {
    "hooks": {
       // 代码提交前，运行单元测试、lint 检查
      "pre-commit": "npm run test:nowatch && npm run lint"
    }
  },
```

