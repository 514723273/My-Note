## 1 变量类型和计算

知识点：
- 变量类型
- 变量计算

### 1.1 变量类型

#### 1.1.1 值类型

```js
let a = 100
let b = a
a = 200
console.log(b)  // 100 互不影响
```

#### 1.1.2 引用类型

对象、数组、函数 都是引用类型。

```js
let a = {
    age: 100
};
let b = a;
b.age = 200
console.log(a.age)  // 两个指针 指向同一对象
```

#### 1.1.3 typeof 运算符

总共**6**种。（分1，2，3记忆。
- undefind
- object
- function
- number
- bool
- string

```js
// 值类型
typeof undefined    // "undefined"
typeof 'abc'        // "string"
typeof 123          // "number"
typeof true         // "bool"

// 引用类型
typeof {}           // "object"
typeof []           // "object"
typeof null         // "object"     // !!!null
typeof console.log  // "function"
```

### 1.2 变量计算

#### 1.2.1 强制类型转换

- 字符串拼接
- == 运算符
- if 语句
- 逻辑运算

##### 1.2.1.1 字符串拼接
```js
let a = 100 + 10    // 110
let b = 100 + '10'  // 10010
```

##### 1.2.1.2 == 运算符

慎用!!!

```js
100 == '100'        // true
0 == ''             // true
null == undefined   // true
```

##### 1.2.1.3 if 语句

```js
let a = true
if(a) {
    // ...  // 执行
}

let b = 100
if(b) {
    // ...  // 执行 
}

let c = ''
if(c) {
    // ...  // 不执行
}
```

##### 1.2.1.4 逻辑运算

```js
console.log(10 && 0)        // 0
console.log('' || 'abc')    // 'abc'
console.log(!window.abc)    // true

// 判断一个变量会被当做 true 还是 false
let a = 100
console.log(!!a)
```

### 1.3 真题

#### 1.3.1 JS 中使用 typeof 能得到的哪些类型

#### 1.3.2 何时使用 === 何时使用 ==

只推荐底下一种写法，其余全`===`：
```js
if(obj.a == null) {
    // 这里相当于 obj.a === null || obj.a ==== undefined 的简写形式
    // 这是 jQuery 源码中推荐的写法
}
```

#### 1.3.3 JS 中有哪些内置函数


```js
// 数据封装类对象
Object
Array
Boolean
Number
String
Function
Date
RegExp
Error
```

#### 1.3.4 JS 变量按照存储方式区分为哪些类型，并描述其特点

值类型和引用类型。

#### 1.3.5 如何理解 JSON 

JSON 只不过是一个 JS 对象而已
```js
JSON.stringify({a: 10, b: 20});
JSON.parse('{"a": 10, "b": 20}');
```

## 2. 原型和原型链

知识点：
- 构造函数
- 构造函数---扩展
- 原型规则和示例
- 原型链
- instanceof

### 2.1 构造函数

```js
function Foo(name, age) {
    this.name = name;
    this.age = age;
    this.class = 'class-1';
    // return this;     // 默认有这一行
}

const foo = new Foo('zhangsan', 20)
// new 一个对象的执行过程
// 1. 传入参数
// 2. this 会变成一个空对象 !!!
// 3. this 属性赋值
// 4. 使用 new 默认返回一个 this 对象，赋值给 foo !!!

// const f1 = new Foo('lisi', 22)      // 创建多个对象
```

#### 2.1.1 new 过程---牛客网的回复

```
作者：菜叽叽
链接：https://www.nowcoder.com/discuss/221126?toCommentId=3471469
来源：牛客网

在 Interview/Content.md 也有 new 过程的具体实现。

1.在堆中开辟对象内存空间, 记为obj
2.在obj 中添加__proto__属性并指向 构造函数.prototype
3.将构造函数中的this 指向obj
4.执行构造函数内语句
5.若构造函数中没有reutrn 或return this或基本类型（number、string、boolean、null、undefined）的值，则返回obj在堆中的内存地址；若return 引用类型，则返回值为这个引用类型
```

### 2.2 构造函数---扩展

- var a = {} 其实是 var a = new Object() 的语法糖
- var a = [] 其实是 var a = new Array() 的语法糖
- function Foo() {...} 其实是 var Foo = new Function(...) 的语法糖

以上都推荐前面的方式。

- 使用 instanceof 判断一个函数是否是一个变量的构造函数 

### 2.3 原型规则和示例

共五条原型规则，原型规则是学习原型链的基础。

1. 所有的引用类型（数组、对象、函数），都具有对象特性，即自由扩展属性（除了 "null" ）

```js
let obj = {}; obj.a = 100;
let arr = []; arr.a = 100;
function fn() {}
fn.a = 100;
```

2. 所有的*引用类型*（数组、对象、函数），都有一个`__proto__`属性（*隐式原型*），属性值是一个普通对象
```js
console.log(obj.__proto__);
console.log(arr.__proto__);
console.log(fn.__proto__);
```
3. 所有的*函数*，都有一个 `prototype` 属性（*显式原型*），属性值也是一个普通的对象
```js
console.log(fn.prototype);
```
4. 所有的*引用类型*（数组、对象、函数），`__proto__` 属性值指向它的*构造函数*的 `prototype` 属性值
```js
console.log(obj.__proto__ === Object.prototype)
```
5. 当试图得到一个对象的某个属性时，如果这个对象本身没有这个属性，那么会去它的 `__proto__` （即它的构造函数的 prototype ）中寻找。
```js
// 构造函数
function Foo(name, age) {
    this.name = name;
}
Foo.prototype.alertName = function() {
    alert(this.name);
}
// 创建示例
let foo = new Foo('zhangsan');
foo.printName = function() {
    console.log(this.name);
}

// 测试
foo.printName();
foo.alertName();      // 去 foo.__proto__ （构造函数的 prototype ）中寻找
```
### 2.4 原型链

```js
foo.toString()        // 要去 foo.__proto__.__proto__ 中查找
```

![原型链](https://raw.githubusercontent.com/514723273/.md-Pictures/master/原型链2.png)

### 2.5 instanceof

用于判断 **引用类型** 属于哪个 **构造函数** 的方法

```js
// foo 的 __proto__ 一层一层往上走，能否找到 Foo.prototype Object.prototype
foo instanceof Foo      //true
foo instanceof Object   // true
```

### 2.6 真题

#### 2.6.1 如何准确判断一个变量时数组类型

```js
let arr = []
arr instanceof Array    // true
typeof arr              // "Object"     // typeof 无法准确判断
```

#### 2.6.2 写一个原型链继承的例子

```js
// low 版

// 动物
function Animal() {
    this.eat = function() {
        console.log('animal eat');
    }
}

// 狗
function Dog() {
    this.bark = function() {
        console.log('dog bark');
    }
}

// !!! 原型链继承 注意是 new Animal() 一个对象，而不是 Animal 构造函数! 
Dog.prototype = new Animal();

// 哈士奇
let hashiqi = new Dog();
```

#### 2.6.3 描述 new 一个对象的过程

- 创建一个新对象
- this 指向这个新对象
- 执行代码，即对 this 赋值
- 返回 this

## 3 作用域和闭包

### 3.1 执行上下文

```js
console.log(a);     // undefined
var a = 100;

fn('zhangsan');     // 'zhangsan' 20
function fn(name) {
    age = 20;
    console.log(name, age);
    var age
}
```

- 范围：一段`<script>`或者一个函数
  - 全局：变量定义、函数声明（一段`<script>`
  - 函数：变量定义、函数声明、this、argument（函数

### 3.2 this

`this` 要在执行时才能确认值，定义时无法确认。

```js
var a = {
    name: 'A',
    fn: function() {
        console.log(this.name);
    }
}

a.fn()                  // this === a
a.fn.call({name: 'B'})  // this === {name: 'B'}
var fn1 = a.fn
fn1()                   // this === window
```

### 3.3 作用域

- 没有块级作用域（ES6 let 实际上为 JavaScript 新增了块级作用域。
- 只有函数和全局作用域

```js
// var name = undefined      // 因为没有块级作用域，提升到最顶
if(true) {
    var name = 'zhangsan';   // 这边赋值
}
console.log(name);          // 'zhangsan'

// 函数和全局作用域
var a = 100;
function fn() {
    var a = 200;
    console.log('fn', a);
}
console.log('global', a);   // 'global', 100
fn()                        // 'fn', 200
```

### 3.4 作用域链

原型链是在当前*对象*中找不到*属性*时，向上寻找。

作用域链是当前*函数*中找不到*变量*时，向上寻找。

```js
var a = 100;
function fn() {
    var b = 200;

    // 当前作用域没有定义的变量，即“自由变量”
    // 自由变量向其父作用域寻找值
    console.log(a);

    console.log(b);
}

fn();
```

### 3.5 闭包

闭包 是指有权访问另一个函数作用域中的变量的**函数**!!!

#### 3.5.1 闭包的使用场景

- 函数作为返回值
- 函数作为参数传递

```js
/**
* 函数作为返回值
**/

function F1() {
    var a = 100;

    // 返回一个函数 （函数作为返回值）
    return function() {
        console.log(a);     // a 是自由变量，向其（定义时的，而不是执行时的!!!）父作用域寻找。所以 a = 100
    }
}

// f1 得到一个函数
var f1 = F1();
var a = 200;
f1();       // 100
```
```js
/**
* 函数作为参数传递
**/

function F1() {
    var a = 100;
    return function() {
        console.log(a);
    }
}
f1 = F1();

function F2(fn) {
    var a = 200;
    fn();
}
F2(f1);     // 100 所以一直是定义时s
```

### 3.6 真题

### 3.6.1 说一下对变量提升的理解

### 3.6.2 说明 this 几种不同的使用场景

- 作为构造函数执行
- 作为对象属性执行
- 作为普通函数执行
- call apply bind

### 3.6.3 创建 10 个 `<a>` 标签，点击的时候弹出来对应的序号

```js
var i;
for(i = 0; i < 10; i ++) {
    (function(i) {
        var a = document.createElement('a');
        a.innerHTML = i + '<br>';
        a.addEventListener('click', function (e) {
            e.reventDefault();
            alert(i);
        });
        document.body.appendChild(a);
    })(i);
}
```

### 3.6.4 如何理解作用域

- 自由变量
- 作用域链，即自由变量的查找
- 闭包的两个场景

## 4 异步和单线程

### 4.1 什么是异步（对比同步）

```js
console.log(100);

setTimeout(function() {
    console.log(200);
}, 1000);

console.log(300);
```

对比同步，同步会发生阻塞。
```js
console.log(100);

alert(200);     // 1 秒后点击确认

console.log(300);
```

### 4.2 前端使用异步的场景

- 定时任务：setTimeout，setInterval
- 网络请求：ajax 请求，动态 `<img>` 加载
- 事件绑定

### 4.3 异步和单线程

```js
console.log(100);

setTimeout(function() {
    console.log(200);
});         // 不设置时间

console.log(300);
```
过程复述：
1. 执行第一行，打印 100
2. 执行 setTimeout 后，传入 setTimeout 的函数会被暂存起来，不会立刻执行（单线程的特点，不能同时干两件事
3. 执行最后一行，打印 300
4. !!!待所有程序执行完，处于空闲状态时，会立马看有没有暂存起来的要执行
5. 发现暂存起来的 setTimeout 中的函数无需等待时间，就立即拿过来执行

### 4.4 真题

#### 4.4.1 同步和异步的区别是什么？分别举一个同步和异步的例子

- 同步会阻塞代码执行，而异步不会
- alert 是同步，setTimeout 是异步

#### 4.4.2 一个关于 setTimeout 的笔试题

```js
console.log(1);

setTimeout(function() {
    console.log(2);
}, 0);

console.log(3);

setTimeout(function() {
    console.log(4);
}, 1000);

console.log(5)

// 1 3 5 2 4
```

#### 4.4.3 前端使用异步的场景有哪些


## 5 其他内置对象和 API 相关的知识

### 5.1 日期

```js
Date.now()      // 获取当前时间的毫秒数

var dt = new Date()
dt.getTime()        // 获取毫秒数
dt.getFullYear()    // 年
dt.getMouth()       // 月   （0-11）
dt.getDate()        // 日   （0-31）
dt.getHours()       // 小时 （0-23）
dt.getMiniutes()    // 分钟 （0-59）
dt.getSeconds()     // 秒   （0-59）
```

### 5.2 Math

- 获取随机数 Math.random()

### 5.3 数组 API

[514723273/After-Reading/JavaScript高级程序设计](https://github.com/514723273/After-Reading/blob/master/JavaScript%E9%AB%98%E7%BA%A7%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1/Content.md#528-%E8%BF%AD%E4%BB%A3%E6%96%B9%E6%B3%95)

- every()，数组中的每一项都满足条件，才返回true。
- some()，数组中只要有一项满足条件，就可以返回true。
- filter()，返回满足条件的所有项组成的数组。
- map()，将数组所有项进行操作，返回操作后的数组。
- forEach，没有返回值，只做遍历操作。

### 5.4 对象 API

```js
var obj = {
    x: 100,
    y: 200,
    z: 300
}

var key;
// for-in 循环时，返回实例属性和原型属性。（能够通过对象访问的、可枚举的属性）
for(key in obj) {
    if(obj.hasOwnProperty(key)) {
        console.log(key, obj[key]);
    }
}
```

### 5.5 真题

#### 5.5.1 获取 2017-07-10 格式的日期

```js
function formatDate(dt) {
    if(!dt) {
        dt = new Date();
    }
    let year = dt.getFullYear();
    let month = dt.getMonth() + 1;
    let date = dt.getDate();

    if(month < 10) {
        month = '0' + month;
    }
    if(date < 10) {
        date = '0' + date;
    }

    return year + '-' + month + '-' + date;
}
```

#### 5.5.2 获取随机数，要求是长度一致的字符串格式

```js
var random = Math.random();
var random = random + '0000000000';     // 后面加上 10 个零
var random = random.slice(0, 10);
console.log(random);
```

#### 5.5.3 写一个能遍历对象和数组的通用 forEach 函数

```js
function forEach(obj, fn) {
    var key;
    if(obj instanceof Array) {
        obj.forEach(function (item, index) {
            fn(index, iem);
        })
    } else {
        for(key in obj) {
            fn(key, obj[key]);
        }
    }
}
```

```js
var arr = [1, 2, 3];
forEach(arr, function(index, item) {
    console.log(index, item);
});

var obj = {x: 100, y: 200};
forEach(obj, function(key, value) {
    console.log(key, value);
})
```

## 6 DOM

Document Object Model

### 6.1 总结以上

常说的 JS （浏览器执行的 JS ）包含两部分:
- JS 基础知识（ ECMA262 标准）
- JS-Web-API ( W3C 标准)

以上是基础知识，接下来是 JS-Web-API 。

### 6.2 DOM 本质

DOM 可以理解为：

浏览器把拿到的 html 代码，结构化一个浏览器能识别并且 js 可以操作的一个模型而已。

### 6.3 DOM 节点操作

#### 6.3.1 获取 DOM 节点

```js
var div1 = document.getElementById('div1');                             // 元素
var divList = document.getElementsByTagName('div')                      // 集合
console.log(divList.length);
console.log(divList[0]);

var containerList = document.getElementsByClassName('.container');      // 集合
```

#### 6.3.2 property

操作 DOM JS 对象的属性。

```js
var pList = document.querySelectorAll('p'); // 集合
var p = pList[0];
console.log(p.style.width);                 // 获取样式
p.style.width = '100px';                    // 修改样式
console.log(p.className);                   // 获取 class

console.log(p.nodeName);                    // 获取 nodeName
console.log(p.nodeType);                    // 获取 nodeType
```

#### 6.3.3 attribute

操作的 html 文档的属性。

```js
var pList = document.querySelectorAll('p');
var p = pList[0];
p.getAttribute('data-name');
p.setAttribute('data-name', 'imooc');
p.getAttribute('style');
p.setAttribute('style', 'font-size: 30px;');
```

### 6.4 DOM 结构操作

#### 6.4.1 新增节点

```js
var div1 = document.getElementById('div1');

// 添加新节点
var p1 = document.createElement('p');
p1.innerHTML = 'this is p1';
div1.appendChild(p1);       // 添加新创建的元素

// 移动已有节点
var p2 = document.getElementById('p2');
div1.appendChild(p2);
```

#### 6.4.2 获取父元素、获取子元素、删除节点

```js
var div1 = document.getElementById('div1');
// 获取父元素
var parent = div1.parentElement;
// 获取子元素
var child = div1.childNodes;
// 删除节点
div1.removeChild(child[0]);
```

### 6.5 真题

#### 6.5.1 DOM 是哪种基本的数据结构？

树。

#### 6.5.2 DOM 操作的常用 API 有哪些

- 获取 DOM 节点，以及节点的 property 和 attribute
- 获取父节点，获取子节点
- 新增节点，删除节点

#### 6.5.3 DOM 节点的 attribute 和 property 有何区别

 - property 只是一个 JS 对象的属性的修改
 - attribute 是对 html 标签属性的修改

## 7 BOM

### 7.1 navigator 浏览器

```js
var ua = navigator.userAgent;
var isChrome = ua.indexOf('Chrome');
console.log(isChrome)
```

### 7.2 screen 屏幕

```js
console.log(screen.width);
console.log(screen.height);
```

### 7.3 location 地址

```js
console.log(location.href);         // 完整的地址
console.log(location.protocol);     // http https
console.log(location.host);         // 域名
console.log(location.pathname);     // 路径 /learn/100
console.log(location.search);       // ?查询参数
console.log(location.hash);         // #后面
```

### 7.4 history

```js
history.back();         // 后退
history.forward();      // 前进
```

### 7.9 真题

#### 7.9.1 如何检测浏览器的类型

```js
var ua = navigator.userAgent
var isChrome = ua.indexOf('Chrome');
console.log(isChrome);
```

#### 7.9.2 拆解 url 的各种部分

参照 7.3

## 8 事件

### 8.1 通用事件绑定

基础写法：
```js
var btn = document.getElementById('btn1');
btn.addEventListener('click', function(event) {
    console.log('clicked');
})
```

封装后的写法：
```js
function bindEvent(elem, type, fn) {
    elem.addEventListener(type, fn);
}

// 好处少些代码，addEventListener 又长又臭
var a = document.getElementById('link1');
bindEvent(a, 'click', function(e) {
    e.preventDefault();         // 阻止默认行为（ a 链接的默认行为是跳转连接（跳转了还怎么弹窗
    alert('clicked');
})
```

### 8.2 事件冒泡

需求：点击 p1 弹窗显示激活，点击其余的 p 弹窗显示取消。

正常想法：为 6 个 p 分辨绑定事件，应该也是重复代码。

事件冒泡：利用事件冒泡，为整个 body 绑定弹窗取消事件，为 p1 绑定弹窗激活事件，并阻止冒泡。这样点击其他就会弹出取消，而 p1 不会。 

```html
<body>
    <div id="div1">
        <p id="p1">激活</p>
        <p id="p2">取消</p>
        <p id="p3">取消</p>
        <p id="p4">取消</p>
    </div>
    <div>
        <p id="p5">取消</p>
        <p id="p6">取消</p>
    </div>
</body>
```

```js
var p1 = document.getElementById('p1');
bindEvent(p1, 'click', function(e) {
    e.stopPropatation();
    alert('激活');
});

var body = document.body;
bindEvent(body, 'click', function(e) {
    alert('取消');
})
```

### 8.3 代理

需求：点击 a 标签，弹出该点击的 a 标签的 innerHTML 。

好处：代码简洁、减少浏览器内存占用。

```html
<div id="div1">
    <a href="#">a1</a>
    <a href="#">a2</a>
    <a href="#">a3</a>
    <a href="#">a4</a>
    <!-- 会随时新增更多 a 标签 -->
</div>
```

```js
var div1 = document.getElementById('div1');
div1.addEventListener('click', function(e) {
    var target = e.target;
    if(target.typeName === 'A') {
        alert(target.innerHTML);
    }
})
```

### 8.4 真题

#### 8.4.1 编写一个通用的事件监听函数

```js
function bindEvent(elem, type, selector, fn) {
    if(fn === null) {
        fn = selector;
        selector = null;
    }
    elem.addEventListener(type, function(e) {
        var target;
        if(selector) {              // 如果有选择器
            target = e.target;      // 点击的事件元素
            if(target.matches(selector)) {
                fn.call(target, e);
            }
        } else {
            fn(e);
        }
    })
}
```

```js
// 使用代理
var div1 = document.getElementById('div1');
bindEvent(div1, 'click', 'a', function(e) {
    console.log(this.innerHTML);        // 差别
});

// 不使用代理
var a1 = document.getElementById('a1');
bindEvent(div1, 'click', function(e) {
    console.log(a1.innerHTML);           // 差别
})
```

#### 8.4.2 简述事件冒泡流程

- DOM 树形结构
- 事件冒泡
- 阻止冒泡
- 冒泡的应用


#### 8.4.2 对于一个无限下拉加载图片的页面，如何给每个图片绑定事件

- 使用代理
- 知道代理的两个优点

## 9 Ajax

### 9.1 XMLHttpRequest

一道真题：手动编写一个 ajax

```js
var xhr = new XMLHttpRequest();
xhr.open("GET", "/api", false);
xhr.onreadystatechange = function() {
    // 这里的函数异步执行
    if(xhr.readyState == 4) {           // 每次 xhr.readyState 状态改变，都会触发其执行
        if(xhr.status == 200) {
            alert(xhr.responseText);
        }
    }
}
xhr.send(null);
```

#### 9.1.1 readyState

- 0 （未初始化）还没有调用 send() 方法
- 1 （载入）已调用 send() 方法，正在发送请求
- 2 （载入完成） send() 方法执行完成，已经接收到全部相应内容
- 3 （交互）正在解析响应内容
- 4 （完成）响应急内容解析完成，可以在客户端调用

#### 9.1.2 状态码

- 2xx 表示成功处理请求。
- 3xx 需要重定向，浏览器直接跳转
- 4xx 客户端请求错误
- 5xx 服务器端错误

## 10 跨域

### 10.1 什么是跨域

浏览器有同源策略，不允许 ajax 访问其他域的接口。

跨域条件：协议、域名、端口，有一个不同就算跨域

### 10.2 可以跨域的三个标签

但是有三个标签允许跨域加载资源

- `<img src=xxx>`
- `<link href=xxx>`
- `<script src=xxx>`

三个标签的使用场景：
- img 用于打点统计，统计网站可能是其他域
- link script 可以使用 CDN CDN 的也是其他域
- script 可以用于 JSONP


### 10.5 跨域的几种实现方式

#### 10.5.1 JSONP

- 加载 http://yourhost/xxx.html
- 不一定服务器端真正有一个 xxx.html 文件
- 服务器可以根据请求，动态生成一个文件，返回
- 同理于 `<script src='http://anotherHost/api.js'>`

- 例如我的网站要访问百度的一个接口
- 百度给一个地址 http://baidu.com/api.js
- 返回内容格式 `callback({x: 100, y: 200})` （可动态生成

我的页面 js 如下，定义一个 callback 函数。当 script 标签拿到百度的返回值后，相当于插入了一段 js 代码，然后回调了刚刚定义的 callback 函数。
```html
<script>
    window.callback = function(data) {
        // 这是我们跨域得到的信息
        console.log(data)
    }
</script>
<script src="http://baidu.com/api.js"></script>
<!-- 以上将返回 callback({x: 100, y: 200}) -->
```

#### 10.5.2 服务器端设置 http header

```java
// java 版本
response.setHeader("Access-Control-Allow-Origin", "http://a.com, http://b.com");
response.setHeader("Access-Control-Allow-Headers", "X-Requested-With");
response.setHeader("Access-Control-Allow-Methods", "PUT, POST, GET, DELETE, OPTIONS");

// 接收跨域的 cookie
response.setHeader("Access-Control-Allow-Credentials", "true");
```

## 11 存储

### 11.1 cookie

- 本身用于客户端和服务端通信（每次请求都会携带
- 但是它有本地存储的功能，于是就被“借用”（只是因为是现成的，就将就用一下
- 使用 document.cookie=... 获取和修改即可

缺点：
- 存储量太小，只有 4kb
- 所有 http 请求都带着，会影响获取资源的效率
- API 简单，需要封装才能用 document.cookie=...

### 11.2 localStorage 和 sessionStorage

- HTML5 专门为存储而设计，最大容量 5M
- API简单易用：`localStorage.setItem(key, value)``localStorage.getItem(ke)`

### 11.9 真题

#### 11.9.1 请描述一下 cookie sessionStorage localStorage 的区别

- 容量大小
- 是否携带到 ajax 中（cookie 每次都要被携带
- API 易用性

## 12 模块化

### 12.1 不使用模块化的情况

- util.js getFormatDate 函数
- a-util.js aGetFormatDate 函数 使用 getFormatDate
- a.js aGetFormatDate

```js
//util.js
function getFormatDate(daste, type) {
    // type === 1 返回 2019-07-10 格式
    // type === 2 返回 2019年7月10日 格式
}
```

```js
// a-util.js
function aGetFormatDate(date) {
    // 要求返回 2019年7月10日 格式
    return getFormatDate(date, 2);
}
```

```js
// a.js
var dt = new Date();
console.log(aGetFormatDate(dt));
```

使用

- 这些代码中的函数必须是全局变量，才能暴露给使用方。全局污染。
- a.js 知道要引用 a-util.js ，但是他知道还需要依赖于 uti.js 吗？所以引用的时候可能会忘记写 uti.js 。
```html
<script src="util.js"></script>
<script src="a-util.js"></script>
<script src="a.js"></script>
```

### 12.2 使用模块化

```js
//util.js
export {
    function getFormatDate(daste, type) {
        // type === 1 返回 2019-07-10 格式
        // type === 2 返回 2019年7月10日 格式
    }
}
```

```js
// a-util.js
var getFormatDate = require('util.js');     // require

export {
    function aGetFormatDate(date) {
        // 要求返回 2019年7月10日 格式
        return getFormatDate(date, 2);
    }
}


```

```js
// a.js
var aGetFormatDate = require('a-util.js');  // require

var dt = new Date();
console.log(aGetFormatDate(dt));
```

使用

- 直接引入一个 a.js ，其他的根据依赖关系自动引用
- 那两个函数，没必要做成全局变量，不会带来污染和覆盖

```html
<script src="a.js"></script>
```

#### 12.2.1 AMD

异步(async)模块(module)规范(define)

- [require.js](requirejs.org) 
- 全局 define 函数
- 全局 require 函数
- 依赖 JS 会自动、异步加载

```js
// main.js
require(['./a.js'], function(a) {
    var date = new Date();
    a.printDate(date);
})
```

```js
// a.js
define(['./a-util.js'], function(aUtil) {
    return {
        printDate: function(date) {
            console.log(aUtitl.aGetFormatDate(date))
        }
    }
})
```

```js
// a-util.js
define(['./util.js'], function(util) {
    return {
        aGetFormatDate: function(date) {
            return util.getFormatDate(date, 2);
        }
    }
})
```

```js
// util.js
define(function() {
    return {
        getFormateDate: function(date, type) {
            if(type === 1) {
                return '2019-07-10'
            }
            if(type === 2) {
                return '2017年7月10日'
            }
        }
    }
})
```

使用

```js
<script src="/require.min.js" data-main="./main.js"></script>
```

#### 12.2.2 CommonJS

nodejs 模块化规范，现在被大量用前端，原因：
- 前端开发依赖的插件和库，都可以从 npm 中获取
- 构建工具的高度自动化，使得使用 npm 的成本非常低
- CommonJS 不会异步加载 JS ，而是同步一次性加载出来

```js
// util.js
module.exports = {
    getFormateDate: function(date, type) {
        if(type === 1) {
            return '2019-07-10'
        }
        if(type === 2) {
            return '2017年7月10日'
        }
    }
}
```

```js
// a-util.js
var util = require('util.js')
module.exports = {
    aGetFormatDate: function(date) {
        return util.getFormatDate(date, 2);
    }
}
```

#### 12.2.3 二者使用场景

- 需要异步加载 JS ，使用 AMD
- 使用了 npm 之后建议使用 CommonJS

## 13 页面加载过程

### 13.1 加载资源的形式

- 输入 url （或跳转页面）加载 html
- 加载 html 中的静态资源，包括 视频 图片 js

### 13.2 加载一个资源的过程

1. 浏览器根据 DNS 服务器得到域名 IP 地址
2. 向这个 IP 的机器发送 http 请求
3. 服务求收到、处理并返回 http 请求
4. 浏览器得到返回内容

### 13.3 浏览器渲染页面的过程

1. 根据 HTML 结构生成 DOM Tree
2. 根据 CSS 生成 CSSOM
3. 将 DOM 和 CSSOM 整合形成 RenderTree
4. 根据 RenderTree 开始渲染和展示
5. 遇到`<script>`时，会执行并阻塞渲染

## 14 性能优化

### 14.1 加载资源优化

- 静态资源的压缩合并（比如 3 个 js 文件，合并成一个 js 文件，减少网络请求数量。删去所有缩进，减少文件体积。
- 静态资源缓存（文件名不变，再次请求时，会使用本地缓存。只有内容改变的时候，链接名称才会改变
- 使用 CDN 让资源加载更快（ CDN 是不同区域的网络优化，比如我在浙江请求 jQuery，那就会从浙江返回
- 使用 SSR 后端渲染，数据直接输出到 HTML 中（其实 jsp php asp 都属于后端渲染

### 14.2 渲染优化

- CSS 放前面，JS 放后面
- 懒加载（图片懒加载、下拉加载更多
- 减少 DOM 查询，对 DOM 查询做缓存
- 减少 DOM 操作，多个操作尽量合并到一起执行（DOM 操作是十分昂贵的
- 事件节流
- 尽早执行操作（如 DOMContentLoaded

#### 14.2.1 懒加载

img 标签中的 src 首先是赋值一个 preview 的图片（很小），后面跟一个属性来说明真实图片的文件名，再在 js 代码中，按需加载这个图片，从而加快页面加载的速度。

```html
<img id="img1" src="preview.png" data-realsrc="abc.png" />
<script type="text/javascript">
    var img1 = document.getElementById('img1');
    img1.src = img1.getAttribute('data-realsrc');
</script>
```

#### 14.2.2 缓存 DOM 查询

```js
// 未缓存
for(let i = 0; i < document.getElementsByTagName('p').length; i ++) {
    // todo
}

// 缓存 DOM 查询
let pList = document.getElementsByTagName('p');
for(let i = 0; i < pList.length; i ++) {
    // todo
}
```

#### 14.2.3 合并 DOM 插入

```js
let listNode = document.getElementById('list');

// 要插入 10 个 li 标签
let fragment = document.createDocumentFragment();
for(let x = 0; x < 10; x ++) {
    let li = document.createElement("li");
    li.innerHTML = "List item " + x;
    fragment.appendChild(li);       // 这里不会触发 dom 操作       
}

listNode.appendChild(fragment);     // 不要一个一个插入 li 标签，而是将其合并汇总至一个 fragment 标签中，再一次性插入 list 中，减少插入操作。
```

#### 14.2.4 事件节流

可能打字很快，没有必要显示前面几个结果。所以就添加一个延时，当用户 100 毫秒后不再输入了，正式触发。

```js
var textarea = document.getElementById('text');
var timeoutId
textarea.addEventListener('keyup', function() {
    if(timeoutId) {
        clearTimeout(timeoutId);
    }
    timeoutId = setTimeout(function() {
        // 触发 change 事件
    }, 100);
})
```

### 14.3 真题

#### 14.3.1 window.onload 和 DOMContentLoaded 的区别

就比如 DOM 渲染 10ms ，图片资源过大 2s 。

```js
window.addEventListener('load', function() {
    // 页面的全部资源加载完才会执行，包括图片、视频等
});

document.addEventListener('DOMContentLoaded', function() {
    // DOM 渲染完即可执行，此时图片、视频还可能没有加载完
})
```

#### 14.3.2 为什么 css 要放在 head 中

css 放在 head 中，先生成 CSSOM ，逐行解析 html 时 可直接渲染对应的样式。

假如放在 body 最底部，那浏览器解析 html 时，会按默认样式渲染一遍，解析到 css 的时候，根据样式又重新渲染一遍。

#### 14.3.3 为什么 js 要放在 body 最下面

遇到`<script>`时，会执行并阻塞渲染。

还有就是 script 标签中的 脚本代码若要操作底下的 dom 结构，因为被阻塞而没有渲染，就拿不到该 dom 节点。

## 15 安全性

### 15.1 XSS 跨站请求攻击

1. 在新浪博客写一篇文章，同时偷偷插入一段 script（攻击代码中，获取 cookie ，发送自己的服务器
2. 发布博客，有人查看博客内容
3. 会把查看者的 cookie 发送到攻击者的服务器

解决办法：前端替换关键字，例如替换 `<` 为 &lt （最好是后端替换

### 15.2 XSRF 跨站请求伪造

1. 你已经登录一个购物网站，正在浏览器商品（该网站付费接口是 xxx.com/pay?id=100 但是没有任何验证
2. 然后你收到一封邮件，隐藏着 img src=“xxx.com/pay?id=100”
3. 你查看邮件的时候，就已经悄悄付费购买了

解决办法：增加验证流程，如输入指纹、密码、短信验证码
