> 参考 *网易考拉前端团队*---[学习 BFC (Block Formatting Context)](https://juejin.im/post/59b73d5bf265da064618731d)

> 参考 *zuopf769/notebook*---[史上最全面、最透彻的BFC原理剖析](https://github.com/zuopf769/notebook/blob/master/fe/BFC%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90/README.md)
## 1 BFC 定义

 Block Formatting Context：块级格式化上下文

如果一个元素符合了成为 BFC 的条件，该元素成为一个隔离了的**独立容器**，元素内部元素会垂直的沿着其父元素的边框排列，和外部元素互不影响。

## 1.1 BFC 的作用范围

BFC 包含创建该上下文元素的所有子元素，但不包括创建了新 BFC 的子元素的内部元素。(只包含直系儿子元素，不包含孙子曾孙子元素（除非在内部继续新建 BFC）)

## 2 BFC 创建方法

- **根元素**或其他包含它的元素
- **浮动** (元素的`float`不为`none`)
- **绝对定位**元素 (元素的`position`为`absolute`或`fixed`)
- **行内块`inline-blocks`**（元素的`display: inline-block`）
- **表格单元格**（元素的`display: table-cell`，HTML 表格单元默认属性）
- `overflow`的值不为`visible`的元素
- **弹性盒子`flex box`**（元素的`display: flex`或`inline-flex`）

比较常见的有：`overflow:hidden`、`float:left/right`、`position:absolute`。（看见马上就想到这个元素成为了 BFC）

## 3 BFC的效果（规则）（为什么要用 BFC）

1. 内部的盒会在垂直方向一个接一个排列；
2. 处于同一个 BFC 中的元素相互影响，可能会发生 margin collapse；
~~3. 每个元素的 margin box 的左边，与容器块 border box 的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此；（padding???）~~
4. BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之亦然；
---
5. 计算 BFC 的高度时，考虑 BFC 所包含的所有元素，**连浮动元素也参与计算**；
6. **BFC 的区域不会与`float`的元素区域重叠**（普通`div`会和`float`重叠）

BFC 可以想象成`<body>`内部再新建一个`body`一样，里面又可以重新排列（前四点就是普通的文档流规则）。

（BFC 对于外边普通兄弟元素，float就是脱离文档流不脱离文本流、position: absolute 就是完全脱离文档流、overflow: hidden 就是 div 独占一行）

（下面应用中讲解了 2、5、6 三条）

## 4 BFC在布局中的应用

### 4.1 防止 margin 重叠

```html
<style>
    p {
        color: #f55;
        background: #fcc;
        width: 200px;
        line-height: 100px;
        text-align:center;
        margin: 100px;
    }
</style>
<body>
    <p>Haha</p>
    <p>Hehe</p>
</body>
```

![margin重叠](https://raw.githubusercontent.com/514723273/.md-Pictures/master/margin重叠.png)

根据BFC的效果**2. 处于同一个 BFC 中的元素相互影响，可能会发生 margin collapse**，`body`就是一个BFC，所以两个`p`处在同一个BFC，所以重叠。

解决办法就是：给其中一个外边包裹一个BFC，使二者处于两个不同的BFC中。

```html
<style>
.wrap {
    overflow: hidden;// 新的BFC
}
</style>

<body>
    <p>Haha</p>
    <div class="wrap">
        <p>Hehe</p>
    </div>
</body>
```

### 4.2 清除内部浮动

```html
<style>
    .par {
        border: 5px solid #fcc;
        width: 300px;
    }
 
    .child {
        border: 5px solid #f66;
        width:100px;
        height: 100px;
        float: left;
    }
</style>
<body>
    <div class="par">
        <div class="child"></div>
        <div class="child"></div>
    </div>
</body>
```

![高度塌陷](https://raw.githubusercontent.com/514723273/.md-Pictures/master/高度塌陷.png)

根据BFC的效果**5. 计算 BFC 的高度时，考虑 BFC 所包含的所有元素，连浮动元素也参与计算**，为了能将浮动元素的高度计算在内撑起父元素的高度---清除浮动，可以将.par设置为BFC。

```html
.par {
    overflow: hidden;
}
```
### 4.3 自适应多栏布局

```html
<style>
    .left-side {
        width: 100px;
        height: 150px;
        float: left;
        background: #f66;
    }
    
    .content {
        height: 200px;
        background: #fcc;
    }
</style>

<body>
    <div class="left-side"></div>
    <div class="content"></div>
</body>
```

![float重叠](https://raw.githubusercontent.com/514723273/.md-Pictures/master/float重叠.png)

`.left-side` 与 `.content` 发生重叠。

根据BFC的效果**6. BFC 的区域不会与`float`的元素区域重叠**，为了不发生重叠，可以将 `.content` 设置为BFC。

而为什么是自适应，只是 .content 宽度未设置，.left-side 宽度不变，.content 宽度可以随着视口变化而变化。