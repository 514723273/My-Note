# 高仿 AntD

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

- inline CSS
- CSS in JS：CSS 不是可编程语言；
  - Styled Component
- Sass/Less（采用）
  - **“_”文件名开头表示 sass 不编译到 css 文件中（Partials）**；导入的时候（@import）不需要添加“\_”
  - 接上，`npx node-sass styles/_variables.scss var.css`编译出来为空的文件
  - 接上，参考第二部分代码

```bash
# 样式文件目录

styles/
	_variables.scss # 各种变量（比如色彩、字体）以及可配置开关
	_mixins.scss # 全局 mixins（任何预处理器的基础特性，使重用各种 CSS 更简单，比如添加 box-shadow）
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

// 只需要在 index.tsx 引入 import "./styles/index.scss" 即可
```



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

## 添加 [normalize.css](https://github.com/necolas/normalize.css)

与 reset.css 都是的比较：

- reset.css 是将所有的浏览器的样式都抹掉
- normalize.css 保护了有用的浏览器的默认样式，为所有样式提供统一的一般性的样式