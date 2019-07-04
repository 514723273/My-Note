## 1 HTML 基础强化

### 1.1 HTML 元素分类

按默认样式分
- 块级 block
- 行内 inline
- inline-block

按内容分
- [W3C 元素分类](https://www.w3.org/TR/html5/dom.html#phrasing-content)

## 1.2 HTML 元素嵌套关系

查询 w3c 文档，以下为基本原则
- 块级元素可以包含行内元素
- 块级元素不一定能包含块级元素（例如 p 元素不能包含 div）
- "行内元素一般不能包含块级元素"（例如 a 元素可以包含 div）（a 的内容模型为 transparent 不参与嵌套计算）

## 1.3 HTML 面试真题

### 1.3.1 doctype 的意义是什么

- 让浏览器以标准模式渲染
- 让浏览器知道元素的合法性

### 1.3.2 HTML XHTML HTML5 的关系

- HTML 属于 SGML
- XHTML 属于 XML ，是 HTML 进行 XML 严格化的结果
- HTML5 不属于 SGML 或者 XML ，比 XHTML 宽松

### 1.3.3 HTML5 有什么变化

- 新的语义化元素（例如 section footer header 还有像语义不强的 i b 就推荐使用了，推荐 em strong
- 表单增强（表单验证等
- 新的 API （离线、音视频、图形、实时通信、本地存储、设备能力
- 分类和嵌套变更

### 1.3.4 em 和 i 有什么区别

- em 是语义化的标签，表强调
- i 是纯样式的标签，表斜体
- HTML5 中 i 不推荐使用，一般用作图标

### 1.3.5 语义化的意义是什么

- 开发者容易理解
- 机器容易理解结构（搜索、读屏软件
- 有助于 SEO
- semantic microdata

### 1.3.6 哪些元素可以自闭和

- 表单元素 input
- 图片 img
- br hr
- meta link

在 HTML5 中结尾是不需要加斜杠的，但是在 XHTML 是一定要加的。（最好加吧 也好看  

### 1.3.7 HTMl 和 DOM 的关系

- HTML 是“死”的（我们只是通过换行缩进使它看起来有结构，但是它只是文本
- DOM 是由 HTML 解析而来的，是“活”的（存储在浏览器内存中的树形结构
- JS 可以维护 DOM

### 1.3.8 attribute 和 property

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

### 1.3.9 form 的作用有哪些

- 直接提交表单
- 使用 submit/reset 按钮
- 便于浏览器保存表单
- 第三方库可以整体提取值
- 第三方库可以进行表单验证