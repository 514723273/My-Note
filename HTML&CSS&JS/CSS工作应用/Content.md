## 1 HTML 基础强化

### 1.1 HTML 元素分类

按默认样式分
- 块级 block
- 行内 inline
- inline-block

按内容分
- [W3C 元素分类](https://www.w3.org/TR/html5/dom.html#phrasing-content)

### 1.2 HTML 元素嵌套关系

查询 w3c 文档，以下为基本原则
- 块级元素可以包含行内元素
- 块级元素不一定能包含块级元素（例如 p 元素不能包含 div）
- "行内元素一般不能包含块级元素"（例如 a 元素可以包含 div）（a 的内容模型为 transparent 不参与嵌套计算）

### 1.3 HTML 面试真题

#### 1.3.1 doctype 的意义是什么

- 让浏览器以标准模式渲染
- 让浏览器知道元素的合法性

#### 1.3.2 HTML XHTML HTML5 的关系

- HTML 属于 SGML
- XHTML 属于 XML ，是 HTML 进行 XML 严格化的结果
- HTML5 不属于 SGML 或者 XML ，比 XHTML 宽松

#### 1.3.3 HTML5 有什么变化

- 新的语义化元素（例如 section footer header 还有像语义不强的 i b 就推荐使用了，推荐 em strong
- 表单增强（表单验证等
- 新的 API （离线、音视频、图形、实时通信、本地存储、设备能力
- 分类和嵌套变更

#### 1.3.4 em 和 i 有什么区别

- em 是语义化的标签，表强调
- i 是纯样式的标签，表斜体
- HTML5 中 i 不推荐使用，一般用作图标

#### 1.3.5 语义化的意义是什么

- 开发者容易理解
- 机器容易理解结构（搜索、读屏软件
- 有助于 SEO
- semantic microdata

#### 1.3.6 哪些元素可以自闭和

- 表单元素 input
- 图片 img
- br hr
- meta link

在 HTML5 中结尾是不需要加斜杠的，但是在 XHTML 是一定要加的。（最好加吧 也好看  

#### 1.3.7 HTMl 和 DOM 的关系

- HTML 是“死”的（我们只是通过换行缩进使它看起来有结构，但是它只是文本
- DOM 是由 HTML 解析而来的，是“活”的（存储在浏览器内存中的树形结构
- JS 可以维护 DOM

#### 1.3.8 attribute 和 property

分别翻译为**属性**和**特性**。

- attribute 是“死”的（直接写在 html 元素中的
- property 是“活”的（浏览器解析后，将属性的值赋给 property

```html
<input type="text" value="1"/>
```
上面写着的 `value="1"` 就是属性 value 为 1 。

在 console 底下调试
```
$0                          // 取当前选中的元素，也就是当前的 input 框。
$0.value                    // "1" 代表属性为 1 ，特性也为 1
$0.value = "2"              // 赋值改变特性，但是没有改变属性！！！（ html 代码中 value="1"
$0.getAttribute('value')    // "1"  利用 api 发现 value 仍为 1
```

#### 1.3.9 form 的作用有哪些

- 直接提交表单
- 使用 submit/reset 按钮
- 便于浏览器保存表单
- 第三方库可以整体提取值
- 第三方库可以进行表单验证

## 2 CSS 选择器

层叠样式表

Cascading Style Sheet 

美 [kæˈskeɪdɪŋ]

![CSS幻灯片类比](../../img/CSS幻灯片类比.png)

### 2.1 基本规则

- 用于匹配 HTML 元素
- 有不同的匹配规则
- 多个选择器可叠加


### 2.2 选择器的分类和权重

#### 2.2.1 分类

1. 元素选择器 a{}
2. 伪元素选择器 ::before{}
3. 类选择器 .link{}
4. 属性选择器 [type=radio]{}
5. 伪类选择器 :hover{}
6. ID 选择器 #id{}
7. 组合选择器 [type=checkbox] + label{}
8. 否定选择器 :not(.link){}
9. 通用选择器：*{}

#### 2.2.2 权重

- ID 选择器 #id{} + 100
- 类 属性 伪类 .link{} [type=radio]{} :hover{} + 10
- 元素 伪元素 a{} ::before{} +1
- 其他选择器 +0

**注意不进位！！！**（看例子

还有一些其他规则：
- !important 优先级最高
- 元素属性 优先级高
- 相同权重 后写的生效

#### 2.2.3 举例

[selector-priority](./example/chapter3/selector-priority.html)

### 解析方式和性能

### 值得关注的选择器

```html
<!-- 
但是浏览器是从后往前解析的，并不是逐渐缩小范围！
而是反过来判断 #hello 的父元素是否是 div 再上一层 id 是否是 app
-->
<head>
    <style>
        #app div .hello {
            color: red;
        }
    </style>
</head>
<body id="app">
    <div>
        <a class="hello">alink</a>
    </div>
</body>
```

## 3 非布局样式

- 字体、字重、颜色、大小、行高
- 背景、边框
- 滚动、换行
- 粗体、斜体、下划线
- 其他

### 3.1 字体

- 字体族
    - serif---------衬线
    - sans-serif----非衬线
    - monospace-----等宽字体
    - cursive-------连笔
    - fantasy-------花体
- 多字体 fallback---如果计算机找不到第一个字体，就使用第二字体，再找不到，第三个...一直找到能用的
- 网络字体、自定义字体
- iconfont

举例：[fonts](./example/chapter3/fonts.html)

### 3.2 行高

#### 3.2.1 行高的构成

![三线说明](../../img/三线说明.png)

每个盒子渲染高度还是相同的。（由字体决定

line-height 会决定上下多余的宽度 会撑起外边的盒子（line-box）。

举例：[inline](./example/chapter3/inline.html)

#### 3.2.2 行高相关的现象和方案

vertical-align 决定对齐方式，针对字体的线而言。默认是基线baseline对齐。

图片是按inline来排版，排版就会涉及到字体对齐。默认是按照基线对齐。所以 img 底下会有一点空隙。

!!!图片 3px 缝隙问题。解决办法：设置对齐方式： vertical-align: bottom

举例：[line-height](./example/chapter3/line-height.html)


### 3.3 背景

#### 3.3.1 背景颜色

- 常见颜色单词
- hsl
- hsla
- rgb
- rgba

#### 3.3.2 渐变色背景

- background: linear-gradient(90deg, red, green); 

#### 3.3.4 背景图片和属性（雪碧图）

- width: 40px;
- height: 40px;
- background: url(./test_bg.png) no-repeat;
- background-position: -338px -46px;

将所有需要的图片拼接成一张图片，减少网络请求，先通过设置 width height 设置窗口大小，再通过 position 位移图片，显示需要显示的部分。

如果还需要使用 background-sise 调整图片大小，再调整了窗口大小，那偏移也得调整相同的比例。

#### 3.3.5 base64 和性能优化

减少 http 请求，但文件的体积会增大三分之一。

一般适合小图标

#### 3.3.6 举例

[background](./example/chapter3/background.html)

[background-image](./example/chapter3/background-image.html)

### 3.4 边框

边框的属性：线形 大小 颜色

边框背景图

边框衔接（三角形）

举例：[border](./example/chapter3/border.html)

### 3.5 滚动

overflow: visible

![滚动条属性](../../img/滚动条属性.png)

### 3.6 文字折行

- overflow-wrap （word-wrap）通用换行控制
  - 是否保留单词
- word-break 针对多字节文字
  - 中文句子也是单词
- white-space 空白处是否断行

### 3.7 装饰性属性及其他

- 字重（粗体）font-weight
- 斜体 font-style: itatic
- 下划线 text-decoration
- 指针 cursor

### 3.8 真题

#### 3.8.1 CSS 样式（选择器）的优先级

#### 3.8.2 雪碧图的作用

#### 3.8.3 base64 的使用

#### 3.8.4 伪类和伪元素的区别？

- 伪类表状态
- 伪元素是真的有元素
- 前者单冒号，后者双冒号

#### 3.8.5 如何美化 checkbox

- label[for] 和 id
- 隐藏原生 input
- :checked + label