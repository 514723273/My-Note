## CSS 选择器的类型

1. ID 选择器 `#id {}`
---
2. 类选择器 `.link{}`
3. 属性选择器 `[id="test"] {}`
4. 伪类选择器 `:hover {}`
---
5. 元素选择器 `a {}`
6. 伪元素选择器 `::before {}`
---
7. 相邻兄弟选择器: `li + li {}`   只会对**直接**相邻的兄弟元素产生效果（只影响两个 li ，第一个 li 无效果，第二个 li 才有影响
8. 通用兄弟选择器 `h2 ~ p`  匹配 h2 元素之后的所有同级元素 p（无论直接相邻与否）
9. 子选择器 `h1 > strong`      匹配 h1 元素的所有**直接**子元素 strong
10. 后代选择器 `div .sidebar `   使用**空格**，用于选择指定标签元素下的后辈元素
11. 选择器分组 `,` 当我们想为HTML中多个标签元素设置同一个样式时，可以使用分组选择符---逗号

## CSS 选择器的优先级是如何计算的？

1. `!important`，加在样式属性值后，权重值为 10000
2. 内联样式，如：`style=""`，权重值为 1000
---
3. ID选择器，如：`#content`，权重值为 100
4. 类，伪类和属性选择器，如： `content`、`:hover` 权重值为 10
5. 元素选择器和伪元素选择器，如：`div`、`p`、`:before` 权重值为 1
6. 通用选择器（*）、子选择器（>）、相邻选择器（+）、同胞选择器（~）、权重值为 0

比较规则：
- [0, 0, 1, 0, 0, 0] 比 [0, 0, 0, 2, 12, 29] 优先级高。
- 当出现优先级相等的情况时，最晚出现的样式规则会被采纳。
- 在写样式时，我会使用**较低的**优先级，这样这些样式可以轻易地覆盖掉。

## 重置（resetting）CSS 和 标准化（normalizing）CSS 的区别是什么？你会选择哪种方式，为什么？

- 重置（Resetting）： 重置意味着除去所有的浏览器默认样式。对于页面所有的元素，像margin、padding、font-size这些样式全部置成一样。你将必须重新定义各种元素的样式。
- 标准化（Normalizing）： 标准化没有去掉所有的默认样式，而是保留了有用的一部分，同时还纠正了一些常见错误。

当需要实现非常个性化的网页设计时，我会选择重置的方式，因为我要写很多自定义的样式以满足设计需求，这时候就不再需要标准化的默认样式了。

## 请阐述Float定位的工作原理。

会从普通文档流中脱离（不会撑其父元素的宽高），但浮动元素影响的不仅是自己（形成块，可以设置宽高），它会影响周围的元素对齐进行环绕（不脱离文本流）。（这一点与绝对定位不同，绝对定位的元素完全从文档流中脱离。）

参考：[详解CSS的float属性](https://github.com/514723273/My-Note/blob/master/HTML%26CSS%26JS/%E8%AF%A6%E8%A7%A3CSS%E7%9A%84float%E5%B1%9E%E6%80%A7.md)

## 请阐述块格式化上下文（Block Formatting Context）及其工作原理。

如果一个元素符合了成为 BFC 的条件，该元素成为一个隔离了的独立容器，元素内部元素会垂直的沿着其父元素的边框排列，和外部元素互不影响。

主要是创建方法、BFC效果规则后两条。

参考[My-Note/HTML&CSS&JS/学习BFC](https://github.com/514723273/My-Note/blob/master/HTML%26CSS%26JS/%E5%AD%A6%E4%B9%A0BFC.md)

## 如何实现一个元素垂直水平居中？

> 参考 [yanhaijing/vertical-center](https://github.com/yanhaijing/vertical-center)，共[十种方式](https://github.com/514723273/Exercise-Project/tree/master/html%26css/%E6%B0%B4%E5%B9%B3%E5%9E%82%E7%9B%B4%E5%B1%85%E4%B8%AD)

区分定高宽和不定高宽的情况，以下一种方法可以解决两种情况：

```html
<style>
    .wrapper {
        position: relative;
        width: 500px;
        height: 500px;
        background: red;
    }
    .content {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: green;
    }
</style>
<body>
    <div class="wrapper">
        <div class="content">需要居中的不定高宽的内容</div>
    </div>
</body>
```

## 请阐述`z-index`属性，并说明如何形成层叠上下文（stacking context）。

CSS 中的`z-index`属性控制重叠元素的垂直叠加顺序。`z-index`只能影响`position`值**不是static的元素**。

没有定义`z-index`的值时，元素按照它们出现在 DOM 中的顺序堆叠（层级越低，出现位置越靠上）。非静态定位的元素（及其子元素）将始终覆盖静态定位（static）的元素，而不管 HTML 层次结构如何。

层叠上下文是包含一组图层的元素。 在一组层叠上下文中，其子元素的`z-index`值是相对于该父元素而不是 document root 设置的。每个层叠上下文完全独立于它的兄弟元素。如果元素 B 位于元素 A 之上，则即使元素 A 的子元素 C 具有比元素 B 更高的`z-index`值，元素 C 也永远不会在元素 B 之上。

每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会在父层叠上下文中按顺序进行层叠。少数 CSS 属性会触发一个新的层叠上下文，例如`opacity`小于 1，`filter`不是`none`，`transform`不是`none`。