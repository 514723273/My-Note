> 参考 [sunshine小小倩---如何使用 Canvas 制作出炫酷的网页背景特效](https://juejin.im/book/5a0ab8e2f265da43111fbab2)
# 1 什么是 Canvas

Canvas 是为了解决 Web 页面中只能显示静态图片这个问题而提出的，一个可以使用 JavaScript 等脚本语言向其中绘制图像的 HTML 标签（只是一个画布，本身并不具有绘图的能力）。

## 1.1 svg 和 Canvas 的区别

简单的说就是，**svg 可以用来定义 XML 格式的矢量图形**。

Canvas | svg
---|---
依赖分辨率（位图） | 不依赖分辨率（矢量图）
单个 HTML 元素 | 每一个图形都是一个 DOM 元素
只能通过脚本语言绘制图形 | 可以通过 CSS 也可以通过脚本语言绘制
不支持事件处理程序 | 支持事件处理程序
弱的文本渲染能力 | 最适合带有大型渲染区域的应用程序（比如谷歌地图）
图面较小，对象数量较大（`>10k`）时性能最佳 | 对象数量较小 (`<10k`)、图面更大时性能更佳

# 2 入门 Canvas

## 2.1 创建 Canvas 画布

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        #canvas {
            background: #000;
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
</body>
</html>
```
在上面的例子中页面上只有一个 Canvas，没有设置宽高，那么会自动创建一个 300 * 150 的画布（单位默认为 `px`）。

改变画布大小的三种方式：
- HTML 设置 `width`、`height`：`<canvas id="canvas" width="400" height="400">`
- CSS 设置 `width`、`height`：`width: 400px; height: 400px`（画圆出现拉伸现象）原来是因为如果使用 CSS 来设置宽高的话，画布就会按照 300 * 150 的比例进行缩放，也就是将 300 * 150 的页面显示在 400 * 400 的容器中。
- JS 动态设置 `width`、`height`。`canvas.width = 400; canvas.height = 400;`

## 2.2 获取 Canvas 对象

获取到 Canvas 的上下文环境：`canvas.getContext(contextType, contextAttributes)`

上下文类型（contextType）（第二个参数不常用）：
- 2d：代表一个二维渲染上下文
- webgl（或"experimental-webgl"）：代表一个三维渲染上下文
- webgl2（或"experimental-webgl2"）：代表一个三维渲染上下文；这种情况下只能在浏览器中实现 WebGL 版本2 (OpenGL ES 3.0)。

```js
var canvas = document.getElementById("canvas");
var context = canvas.getContext("2d");
```

## 2.3 绘制路径

方法 | 描述
---|---
`fill()` | 填充路径
`stroke()` | 描边
`arc()` | 创建圆弧
`rect()` | 创建矩形
`fillRect()` | 绘制矩形路径区域
`strokeRect()` | 绘制矩形路径描边
`clearRect()` | 在给定的矩形内清除指定的像素
`arcTo()` | 创建两切线之间的弧/曲线
`beginPath()` | 起始一条路径，或重置当前路径
`moveTo()` | 把路径移动到画布中的指定点，不创建线条
`lineTo()` | 	添加一个新点，然后在画布中创建从该点到最后指定点的线条
`closePath()` | 创建从当前点回到起始点的路径
`clip()` | 从原始画布剪切任意形状和尺寸的区域
`quadraticCurveTo()` | 创建二次方贝塞尔曲线
`bezierCurveTo()` | 创建三次方贝塞尔曲线
`isPointInPath()` | 如果指定的点位于当前路径中，则返回 true，否则返回 false

### 2.3.1 画一个点

```js
context.beginPath();       // 起始一条路径，或重置当前路径
context.arc(100, 100, 1, 0, Math.PI * 2, true);  // 创建弧/曲线
context.closePath();       // 创建从当前点回到起始点的路径
context.fillStyle = 'rgb(255,255,255)'; // 设置或返回用于填充绘画的颜色、渐变或模式
context.fill();            // 填充当前绘图（路径）

// 以上也是在 Canvas 中绘图的大致步骤
```

### 2.3.2 绘制弧/曲线

```js
// arc() 方法创建弧/曲线（用于创建圆或部分圆）。
context.arc(x, y, r, sAngle, eAngle, counterclockwise);
// x：圆心的 x 坐标
// y：圆心的 y 坐标
// r：圆的半径
// sAngle：起始角，以弧度计（弧的圆形的三点钟位置是 0 度）
// eAngle：结束角，以弧度计
// counterclockwise：可选。规定应该逆时针还是顺时针绘图。false 为顺时针，true 为逆时针
```

![canvas-arc](https://raw.githubusercontent.com/514723273/.md-Pictures/master/canvas-arc.png)

`stroke()` ：描边，`strokeStyle` 属性设置描边颜色。
`fill()` ：填充，`fillStyle` 属性设置填充颜色。

### 2.3.3 绘制直线

```js
context.beginPath();
// 把路径移动到画布中的指定点，不创建线条
context.moveTo(50, 50);
// 添加一个新点，然后在画布中创建从该点到最后指定点的线条
context.lineTo(100, 100);
context.strokeStyle = '#fff';
context.stroke();
```

在绘制了直线之后，给绘制的直线添加样式：
样式 | 描述
---|---
`lineCap` | 设置或返回线条的结束端点样式（`context.lineCap = 'round';` 增加“圆角”效果）
`lineJoin` | 设置或返回两条线相交时，所创建的拐角类型（）
`lineWidth` | 设置或返回当前的线条宽度（`context.lineWidth = 10;` 宽度设置为 10）
`miterLimit` | 设置或返回最大斜接长度

### 2.3.4 绘制矩形

```js
context.beginPath();

// 绘制一个实心矩形
context.fillStyle = '#fff';
context.fillRect(10, 10, 100, 100);
// 绘制一个空心矩形
context.strokeStyle = '#fff';
context.strokeRect(130, 10, 100, 100);
```

## 2.4 颜色、样式和阴影

给路径设置属性来改变其样式：
属性 | 描述
---|---
`fillStyle` | 设置或返回用于填充绘画的颜色、渐变或模式
`strokeStyle` | 设置或返回用于笔触的颜色、渐变或模式
`shadowColor` | 设置或返回用于阴影的颜色
`shadowBlur` | 设置或返回用于阴影的模糊级别
`shadowOffsetX` | 设置或返回阴影距形状的水平距离
`shadowOffsetY` | 设置或返回阴影距形状的垂直距离

### 2.4.1 设置阴影

```js
context.beginPath();
context.arc(100,100,50,0,2*Math.PI,false);
context.fillStyle = '#fff';

// 设置模糊指数
context.shadowBlur = 20;
// 设置阴影颜色
context.shadowColor = '#fff';

context.fill();
```

### 2.4.2 设置渐变

方法 | 描述
---|---
`createLinearGradient()` | 创建线性渐变（用在画布内容上）
`createPattern()` | 在指定的方向上重复指定的元素
`context.createLinearGradient(x0,y0,x1,y1)` | 创建放射状/环形的渐变（用在画布内容上）（开始渐变坐标(x0, y0) 结束渐变坐标(x1, y1)）（主要）
`addColorStop()` | 规定渐变对象中的颜色和停止位置

```js
// 一个粉色到白色的由上向下的渐变

var grd = context.createLinearGradient(100,100,100,200);   //(100, 100) -> (100, 200) 这两个点的连线实际上就是渐变的方向。（垂直方向）
grd.addColorStop(0,'pink');
grd.addColorStop(1,'white');

context.fillStyle = grd;
context.fillRect(100,100,200,200);  // 左上角坐标 (100, 100) 右下角坐标(200, 200)
```

## 2.5 图形转换

方法 | 描述
---|---
`scale()` | 缩放当前绘图至更大或更小
`rotate()` | 旋转当前绘图
`translate()` | 重新映射画布上的 (0,0) 位置
`transform()` | 替换绘图的当前转换矩阵
`setTransform()` | 将当前转换重置为单位矩阵，然后运行 `transform()`

### 2.5.1 缩放

```js
// 绘制一个矩形
context.strokeStyle = 'white';
context.strokeRect(5,5,50,25);
// 放大到 200%，再次绘制矩形
context.scale(2,2);
context.strokeRect(5,5,50,25);
// 放大到 200%，然后再次绘制矩形
context.scale(2,2);
context.strokeRect(5,5,50,25);
```

### 2.5.2 旋转

```js
context.fillStyle = 'white';
context.rotate(20*Math.PI/180);     // angle : 旋转角度，以弧度计。 如需将角度转换为弧度，请使用 degrees*Math.PI/180 公式进行计算。
context.fillRect(70,30,200,100);

// 注意：比如我对画布使用了 rotate(20*Math.PI/180) 方法，就是将画布旋转了 20°，然后之后绘制的图形都会旋转 20°。
```

## 2.6 图像绘制

方法 | 描述
---|---
`drawImage()` | 向画布上绘制图像、画布或视频

`context.drawImage(img,sx,sy,swidth,sheight,x,y,width,height);` 
- `img`：规定要使用的图像、画布或视频
- `sx`：可选。开始剪切的 x 坐标位置
- `sy`：可选。开始剪切的 y 坐标位置
- `swidth`：可选。被剪切图像的宽度
- `sheight`：可选。被剪切图像的高度
- `x`：在画布上放置图像的 x 坐标位置
- `y`：在画布上放置图像的 y 坐标位置
- `width`：可选。要使用的图像的宽度（伸展或缩小图像）
- `height`：可选。要使用的图像的高度（伸展或缩小图像）

# 3 酷炫背景特效的通性

- 背景[uigradients](https://uigradients.com/#MangoPulp)
  - 单一颜色
  - 渐变
  - 平铺
- 炫酷
  - 动
  - 随机
- 特效（与用户交互）
  -  鼠标跟随
  -  视觉差

# 4 怎么实现随机粒子

## 4.1 创建全屏的 Canvas

得到一个全屏的并且为黑色的 Canvas。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        html,body {
            margin:0;
            overflow:hidden;
            width:100%;
            height:100%;
            cursor:none;
            background:black;
        }
    </style>
</head>
<body>
<canvas id="canvas">

</canvas>
<script>
    // 使用 WIDTH、HEIGHT 两个常量储存屏幕宽度和高度信息
    const WIDTH = document.documentElement.clientWidth;
    const HEIGHT = document.documentElement.clientHeight;
    
    let canvas = document.getElementById("canvas");
    let context = canvas.getContext("2d");

    canvas.width = WIDTH;
    canvas.height = HEIGHT;
</script>
</body>
</html>
```

## 4.2 设置 `RoundItem` 类

```js
function RoundItem(index, x, y) {
    this.index = index;     // 标识唯一
    this.x = x;             // x 坐标
    this.y = y;             // y 坐标
    this.radius = Math.random() * 2 + 1;    // 半径随机
    this.color = `rgba(255, 255, 255, ${(Math.floor(Math.random() * 10) + 1) / 10 / 2})`
}
```

## 4.3 设置 `draw()` 方法

```js
RoundItem.prototype.draw = function() {
    context.fillStyle = this.color;
    context.shadowBlur = this.radius * 2;
    context.beginPath();
    context.arc(this.x, this.y, this.radius, 0, 2 * Math.PI, false);
    context.closePath();
    context.fill();
};
```

## 4.3 设置初始化 `init()` 函数

```js
const items = [];
const initRoundPopulation = 100;
const init = () => {
    for(let i = 0; i < initRoundPopulation; i ++) {
        items[i] = new RoundItem(i, Math.random() * WIDTH, Math.random() * HEIGHT);
        items[i].draw();
    }
}

init();
```

# 5 使你的随机粒子动起来

Canvas 制作动画是一个不断擦除再重绘的过程。

## 5.1 `animate()` 函数

`context.clearRect(x,y,width,height)`
- x：要清除的矩形左上角的 x 坐标
- y：要清除的矩形左上角的 y 坐标
- width：要清除的矩形的宽度，以像素计
- height：要清除的矩形的高度，以像素计
- 全屏幕清除`content.clearRect(0, 0, WIDTH, HEIGHT);`

`requestAnimationFrame()`
- 告诉浏览器，你希望执行动画，并请求浏览器调用指定的函数在下一次重绘之前更新动画。该方法使用一个回调函数作为参数，这个回调函数会在浏览器重绘之前调用。
- `setTimeout` 和 `setInterval` 的问题是都不精确。

`requestAnimationFrame()` 函数可以说是专门用来写动画的。那么 `requestAnimationFrame()` 有什么优点呢？

> 编写动画循环的关键是要知道延迟时间多长合适。一方面，循环间隔必须足够短，这样才能让不同的动画效果显得平滑流畅；另一方面，循环间隔还要足够长，这样才能确保浏览器有能力渲染产生的变化。
>
> 大多数电脑显示器的刷新频率是 60Hz，大概相当于每秒钟重绘 60 次。大多数浏览器都会对重绘操作加以限制，不超过显示器的重绘频率，因为即使超过那个频率用户体验也不会有提升。因此，最平滑动画的最佳循环间隔是 1000ms/60，约等于 16.6ms。
>
> `requestAnimationFrame` 采用系统时间间隔，保持最佳绘制效率，不会因为间隔时间过短，造成过度绘制，增加开销；也不会因为间隔时间太长，使动画卡顿不流畅，让各种网页动画效果能够有一个统一的刷新机制，从而节省系统资源，提高系统性能，改善视觉效果。

```js
// 使用 requestAnimationFrame() 函数**递归**的调用 animate() 函数来实现动画的效果
const animate = () => {
    context.clearRect(0, 0, WIDTH, HEIGHT);

    for(let item of items) {
        item.move();
    }
    // 递归重绘
    requestAnimationFrame(animate);
}
```

## 5.2 创建`move()`函数

使用 move() 函数来改变 item 的 y 坐标，实现匀速上升。

```js
RoundItem.prototype.move = function() {
    this.y -= 0.15;
    // 设置边界条件，代表该 item 已经超出了屏幕
    if(this.y <= -10) {
        this.y = HEIGHT + 10;   // 将其移动到屏幕的最底端重新来，看起来就是有源源不断的粒子
    }
    this.draw();
}
```

## 5.3 在 `init()` 中加入 `animate()`

```js
const init = () => {
    // ...
    animate();
}
```

# 6 使你的鼠标和屏幕互动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
        /*铺满屏幕*/
        body {
            padding: 0;
            margin: 0;
        }

        #canvas {
            background: #000;
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
    <script>
        /* 1. 初始化 Canvas */
        const WIDTH = document.documentElement.clientWidth;
        const HEIGHT = document.documentElement.clientHeight;
        // 拿到 canvas
        const canvas = document.getElementById("canvas");
        canvas.width = WIDTH;
        canvas.height = HEIGHT;
        // 拿到 canvas 上下文
        const canvasCtx = canvas.getContext("2d");

        /* 2. 初始化产生圆的配置 */
        const BASIC_HUE = Math.random() * 360;      // 初始的随机基础色调
        const BASIC_RADIUS = 0.8;                   // 初始半径
        const MAX_RADIUS = 10;                       // 限制圆的最大半径，达到即消失

        const roundItems = [];                      // 存放所有圆点
        // 监听鼠标移动产生圆
        window.onmousemove = event => {
            roundItems.push({
                x: event.clientX,
                y: event.clientY,
                radius: BASIC_RADIUS,
            });
        }

        /* 3. 动画 */
        let nowHue = BASIC_HUE;
        const animate = () => {
            canvasCtx.clearRect(0, 0, WIDTH, HEIGHT);

            nowHue += 0.1;      // 设置颜色渐变
            const nowColor = `hsl(${nowHue}, 100%, 80%)`;

            for(let i = 0; i < roundItems.length; i ++) {
                // 画圆
                canvasCtx.beginPath();
                canvasCtx.arc(roundItems[i].x, roundItems[i].y, roundItems[i].radius, 0, Math.PI * 2);
                canvasCtx.closePath();
                canvasCtx.fillStyle = nowColor;
                canvasCtx.fill();

                // 计算下次圆的半径
                roundItems[i].radius += BASIC_RADIUS;
                if(roundItems[i].radius > MAX_RADIUS) {
                    roundItems.splice(i, 1);
                    i --;
                }
            }
            window.requestAnimationFrame(animate);      // 递归执行
        };

        animate();
    </script>
</body>
</html>
```

# 7 使你的 Canvas 更加优雅

## 7.1 常见的 Canvas 优化方法

1. 避免浮点数的坐标点
2. 使用多层画布去画一个复杂的场景：将经常移动的元素和不经常移动的元素分层，避免不必要的重绘。
3. 用 CSS `transform` 特性缩放画布：使用 `transform` 中的 `translateX/Y` 来切换动画，你将会发现，这并不会触发 `paint` 和 `layout`，仅仅会触发 `composition` 的阶段。这是因为 `transform` 调用的是 GPU 而不是 CPU。
4. 离屏渲染：就是设置缓存，绘制图像的时候在屏幕之外的地方绘制好。

## 7.2 详细介绍其中的离屏渲染

建立两个 Canvas 标签，大小一致，一个正常显示，一个隐藏（缓存用的，不插入 DOM 中）。

先将结果 draw 到缓存用的 canvas 上下文中，因为游离 Canvas 不会造成 UI 的渲染，所以它不会展现出来；

再把缓存的内容整个裁剪再 draw 到正常显示用的 Canvas 上，这样能优化不少。

### 7.2.1 设置开关

我们首先要在全局设置一个变量 `useCache` 来存放我们是否使用离屏渲染这种优化方式。

```js
var useCache = true;
```

### 7.2.2  `Round_item` 方法

然后我们在 `Round_item` 原型的 `draw()` 方法中创建每一个离屏的小的 `canvas`。

```js
function Round_item(index, x, y) {
    this.index = index;
    this.x = x;
    this.y = y;
    this.useCache = useChache;
    
    this.cacheCanvas = document.createElement("canvas");
    this.cacheCtx = this.cacheCanvas.getContext("2d");

    this.r = Math.random() * 2 + 1;
    
    // 我们创建的 cacheCanvas 不仅仅是有圆，还包括圆的阴影，
    // 所以我们要将 cacheCanvas 的面积设置得稍微大一些，
    // 这样才能将圆带阴影一起剪切到我们的主 Canvas 中。
    this.cacheCtx.width = 6 * this.r;
    this.cacheCtx.height = 6 * this.r;
    
    var alpha = (Math.floor(Math.random() * 10) + 1) / 10 / 2;
    this.color = "rgba(255,255,255," + alpha + ")";

    if(useChache){
        this.cache();
    }
}
```

### 7.2.3 `this.cache()` 方法 

在 `this.cache()` 方法中，我们的主要任务是在每一个 `cacheCanvas` 中都绘制一个圆。

```js
    Round_item.prototype.cache = function () {
        this.cacheCtx.save();
        this.cacheCtx.fillStyle = this.color;
        this.cacheCtx.shadowColor = "white";
        this.cacheCtx.shadowBlur = this.r * 2;
        this.cacheCtx.beginPath();
        this.cacheCtx.arc(this.r * 3, this.r * 3, this.r, 0, 2 * Math.PI);
        this.cacheCtx.closePath();
        this.cacheCtx.fill();
        this.cacheCtx.restore();
    };
```

这里需要注意的是，和在 `draw()` 方法中画的圆不同之处是，要注意这里设置的圆心坐标，是 `this.r * 3`，因为我们创建的 `cacheCanvas` 的宽度和高度都是 `6 * this.r`，我们的圆是要显示在 `cacheCanvas` 的正中心，所以设置圆心的坐标应该是 `this.r * 3,this.r * 3`。


### 7.2.4 `draw()` 方法

既然设置了 `cacheCanvas`，那么我们在 `draw()` 中，就需要使用 Canvas 的 `drawImage` 方法将 `cacheCanvas` 中的内容显示在屏幕上。

```js
    Round_item.prototype.draw = function () {
        // 这里也是要判断下，如果没有使用缓存的话，还是使用最原始的创建圆的方式。
        if(!useChache){
            content.fillStyle = this.color;
            content.shadowBlur = this.r * 2;
            content.beginPath();
            content.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
            content.closePath();
            content.fill();
        }else{
            content.drawImage(this.cacheCanvas, this.x - this.r, this.y - this.r);
        }

    };
```