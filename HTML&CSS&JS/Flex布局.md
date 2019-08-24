> [阮一峰---Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
> [阮一峰---Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

## 1 Flex 布局是什么？

意为"弹性布局"，用来为盒状模型提供最大的灵活性。

```css
/* 任何一个容器 */
.box {
    display: flex;
}

/* 行内元素 */
.box {
    display: inline-flex;
}
```

**注意**，设为 Flex 布局以后，子元素的 `float`、`clear` 和 `vertical-align` 属性将失效。

## 2 基本概念

采用 Flex 布局的元素（display: flex | inline-flex），称为 Flex **容器**（flex container），简称"容器"。

它的所有*子元素*自动成为容器成员，称为 Flex **项目**（flex item），简称"项目"。

![flex](https://raw.githubusercontent.com/514723273/.md-Pictures/master/flex.png)

容器默认存在两根轴：
- 水平的主轴（main axis）。（主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end）
- 垂直的交叉轴（cross axis）。交叉轴的开始位置叫做cross start，结束位置叫做cross end。

项目默认沿**主轴**排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

## 3 容器的属性

以下6个属性设置在**容器**上：
1. flex-direction：决定**主轴的方向**（即项目的排列方向）（ `flex-direction: row | row-reverse | column | column-reverse;` ）
    - row（默认）：主轴为水平方向，起点在左端。
    - row-reverse：主轴为水平方向，起点在右端。
    - column：主轴为垂直方向，起点在上沿。
    - column-reverse：主轴为垂直方向，起点在下沿。
2. flex-wrap：定义如果一条轴线排不下，如何**换行**。
    - nowrap（默认）：不换行。
    - wrap：换行，第一行在上方。
    - wrap-reverse：换行，第一行在下方。
3. ~~flex-flow：是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。`flex-flow: <flex-direction> || <flex-wrap>;`~~
4. justify-content：定义了项目在主轴上的**对齐**方式。（水平）
    - flex-start（默认值）：左对齐
    - flex-end：右对齐
    - center：居中
    - space-between：两端对齐，项目之间的间隔都相等。
    - space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。
5. align-items：属性定义项目在**交叉轴上如何对齐**。（垂直）
    - flex-start：交叉轴的起点对齐。
    - flex-end：交叉轴的终点对齐。
    - center：交叉轴的中点对齐。
    - baseline: 项目的第一行文字的基线对齐。
    - stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。
6. align-content：属性定义了**多根轴线的对齐方式。**如果项目只有一根轴线，该属性不起作用。（所有item相对于容器）
   - flex-start：与交叉轴的起点对齐。
   - flex-end：与交叉轴的终点对齐。
   - center：与交叉轴的中点对齐。
   - space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
   - space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
   - stretch（默认值）：轴线占满整个交叉轴。

## 4 项目的属性

以下6个属性设置在**项目**上：
1. order：属性定义项目的排列顺序。
    - 数值：数值越小，排列越**靠前**，默认为0。
2. flex-grow：属性定义项目的放大比例。
    - 数值：默认为0，即如果存在剩余空间，也不**放大**。
3. flex-shrink：定义了项目的缩小比例。
    - 数字：默认为1，即如果空间不足，该项目将**缩小**。
4. flex-basis：定义了在分配多余空间之前，项目占据的主轴空间（main size）。
   - length：可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
   - auto（默认值）：项目的本来大小。
5. ~~flex：是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。~~
6. align-self：允许单个项目有与其他项目不一样的对齐方式。（与容器align-items属性对应）
   - auto
   - flex-start
   - center
   - baseline
   - stretch