# GAMES101: 现代计算机图形学入门0 基础

### 0.1 面向对象

- 封装
- 继承
- 多态

### 0.2 五大设计原则

1. S - 单一职责原则（The Single-Responsibility Principle）
    - 一个程序只做好一件事
    - 如果功能过于复杂就拆分开，每个部分保持独立
2. O - 开放封闭原则（The Open/Closed Principle）（软件设计的终极目标）
    - 对扩展开放，对修改封闭
    - 增加需求时，扩展型代码，而不是修改已有代码
3. L - 里氏置换原则（The Liskov Substitution Principle）（sʌbstɪ'tuʃ(ə)n）
   - 子类能覆盖父类
   - 父类能出现的地方，子类就能出现
4. I - 接口独立原则（The Interface Segregation Principle）（seɡrɪˈɡeɪʃn）
    - 保持结构的单一独立，避免出现“胖接口”
    - JS 中没有接口（typescript 例外），使用较少
    - 类似于单一职责原则，这里更关注接口
5. D - 依赖倒置原则（The Dependency-Inversion Principle）
    - 面向接口编程，依赖于抽象而不依赖于具体
    - 使用方只关注接口而不关注具体类的实现


在 JS 中（弱类型&继承使用较少）， S O 体现较多， L I D 体现较少。

#### 0.3 介绍 23 种设计模式

**1 创建型**

序号|模式名称|描述|
---|--- | ---
1* | [工厂模式（Factory）](#1-%e5%b7%a5%e5%8e%82%e6%a8%a1%e5%bc%8f)| （抽象工厂模式）（工厂方法模式）封装 new 操作
2* | [单例模式（Singleton）](#2-%e5%8d%95%e4%be%8b%e6%a8%a1%e5%bc%8f)| （Java）私有构造方法、（JS）闭包
3 | [原型模式（Prototype）](#10-%e5%8e%9f%e5%9e%8b%e6%a8%a1%e5%bc%8f)| 创建对象开销大；（Java）clone、（JS）Object.create()

**2 结构型**

序号|模式名称|描述|
---|--- | ---
4* | [适配器模式（Adapter）](#3-%e9%80%82%e9%85%8d%e5%99%a8%e6%a8%a1%e5%bc%8f)| 旧接口格式和使用者不兼容
5* | [装饰器模式（Decorator）](#4-%e8%a3%85%e9%a5%b0%e5%99%a8%e6%a8%a1%e5%bc%8f)| 包装增加新功能，原有的对象还是可用
6* | [代理模式（Proxy）](#5-%e4%bb%a3%e7%90%86%e6%a8%a1%e5%bc%8f)| 外边完全封装一层，由外层控制能访问内层的属性
7 | [外观模式（Facade）](#6-%e5%a4%96%e8%a7%82%e6%a8%a1%e5%bc%8f)| 为多个子系统中的一组接口提供了一个高层接口
8 | [桥接模式（Bridge）](#11-%e6%a1%a5%e6%8e%a5%e6%a8%a1%e5%bc%8f)| 把抽象化与实现化解耦；使得二者可以独立变化（颜色、图案分离，组合画图）
9 | 组合模式（Composite）| 把多个对象组成树状结构来表示局部与整体，可以相同地对待单个对象和对象的组合。（虚拟 dom、文件夹）
10 |  享元模式（Flyweight）| 共享内存（考虑内存而非效率）；相同的数据，共享使用

**3 行为型**

序号|模式名称|描述|
--- | --- | ---
11* | [观察者模式（Observer）](#7-%e8%a7%82%e5%af%9f%e8%80%85%e6%a8%a1%e5%bc%8f)| Observer 向 Subject 注册；Subject 状态改变，遍历 Observer 数组，批量修改 update
12* | [状态模式（State）](#9-%e7%8a%b6%e6%80%81%e6%a8%a1%e5%bc%8f)| 一个对象有状态变化，由状态控制主体 `redState.handle(context);`
13 | [策略模式（Strategy）](#11-%e7%ad%96%e7%95%a5%e6%a8%a1%e5%bc%8f) | 不同策略分开，避免出现大量 if...else（不同等级的用户购买）
14 | [模板方法模式（Template method） ](#12-%e6%a8%a1%e6%9d%bf%e6%96%b9%e6%b3%95%e6%a8%a1%e5%bc%8f)| （维基）模板方法模式准备一个抽象类，将部分逻辑以具体方法及具体构造子类的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。
15 | [迭代器模式（Iterator）](#8-%e8%bf%ad%e4%bb%a3%e5%99%a8%e6%a8%a1%e5%bc%8f)| 提供一种方法顺序访问一个聚合对象中各个元素, 而又不需暴露该对象的内部表示。
16 | [责任链模式（Chain of responsibility）](#13-%e8%b4%a3%e4%bb%bb%e9%93%be%e6%a8%a1%e5%bc%8f) | 一步操作可能分为多个职责角色，然后用一个链串起来，来完成；将发起者和各个处理者隔离（组长经理总监批准请假）
17 | [命令模式（Command）](#14-%e5%91%bd%e4%bb%a4%e6%a8%a1%e5%bc%8f) | 发送者->命令者->接受者
18 | [备忘录模式（Memento）](#15-%e5%a4%87%e5%bf%98%e5%bd%95%e6%a8%a1%e5%bc%8f) | 随时记录一个对象的状态变化；随时可以恢复之前的某个状态（撤销）
19 |  访问者模式（Visitor） | （粗略）将数据操作和数据结构进行分离
20 | [中介者模式（Mediator）](#16-%e4%b8%ad%e4%bb%8b%e8%80%85%e6%a8%a1%e5%bc%8f) | 使用一个中介者，使两两关联的多个对象分离。（见底下图）
21 | 解释器模式（Interpreter） | （粗略）描述语言语法如何定义，解释和编译；用于专业场景

## 1 工厂模式

- 将 new 操作单独封装
- 遇到 new 时，就要考虑是否该使用工厂模式

![工厂模式](https://raw.githubusercontent.com/514723273/.md-Pictures/master/工厂模式.png)

```js
class Product {
    constructor(name) {
        this.name = name;
    }
    init() {
        alert('init');
    }
    fun1() {
        alert('fun1');
    }
    fun2() {
        alert('fun2');
    }
}

class Creator {
    create(name) {
        return new Product();
    }
}

// test
const creator = new Creator();
const product = creator.create("my_product");
product.init();
product.fun1();
```

场景：jQuery、React.createElement、Vue 异步组件

## 2 单例模式

- 系统中被唯一使用
- 一个类只有一个实例

```js
class SingleObject {
    login() {
        console.log('login...');
    }
}

SingleObject.getInstance = (function() {
    let instance;
    return function() {
        if(instance == null) {
            instance = new SingleObject();
        }
        return instance;
    }
})();       // 闭包

const obj1 = SingleObject.getInstance();
obj1.login();
const obj2 = SingleObject.getInstance();
obj2.login();

console.log('obj1 === obj2', obj1 === obj2);        // true

// js中无法简单控制
const obj3 = new SingleObject();
obj3.login();   
console.log('obj1 === obj3', obj1 === obj3);        // false

```

场景：jQuery只有一个$、登陆框模拟、购物车、redux 中的 store
```js
// 1. jQuery只有一个$
if(window.jQuery != null) {
    return window.jQuery;
} else {
    // 初始化...
}

//2. 登陆框模拟
class LoginForm {
    constructor() {
        this.state = 'hide';
    }
    show() {
        if(this.state === 'show') {
            alert('已经显示');
            return;
        }
        this.state = 'show';
        console.log('登陆框显示成功');
    }
    hide() {
        if(this.state === 'hide') {
            alert('已隐藏');
            return;
        }
        this.state = 'hide';
        console.log('登陆框隐藏成功');
    }
}

LoginForm.getInstance = function() {
    let instance;

    return function() {
        if(instance == null) {
            instance = new LoginForm;
        }
        return instance;
    }
}();

const loginForm1 = new LoginForm();
loginForm1.show();
const loginForm2 = new LoginForm();
loginForm2.show();
loginForm2.hide();
console.log(loginForm1 === loginForm2);
```

## 3 适配器模式

- 旧接口格式和使用者不兼容
- 中间加一个适配器转换接口

```js
// 需要被适配的
class Adaptee {
    specificRequest() {
        return '德国标准的插头';
    }
}

// 适配器
class Target {
    constructor() {
        this.adaptee = new Adaptee();
    }
    request() {
        const info = this.adaptee.specificRequest();
        return `${info} -> 转换器 -> 中国标准的插头`;
    }
}

// 测试
const target = new Target();
console.log(target.request());
```

场景：封装旧接口、vue computed

## 4 装饰器模式

- 为对象添加新功能
- 不改变其原有的结构和功能

手机壳就是一个装饰器，保证手机正常工作的情况下（上网、电话，也不遮住摄像头和按钮），提供保护的功能。

```js
class Circle {
    draw() {
        console.log("画一个圆形");
    }
}

// 装饰器
class Decorator {
    constructor(circle) {
        this.circle = circle;
    }

    setRedColor() {
        console.log("将画笔设置为红色");
    }

    draw() {
        this.setRedColor();
        this.circle.draw();
    }
}

// test
const circle = new Circle();
circle.draw();

const decorator = new Decorator(circle);
decorator.draw();
```

场景：ES7 装饰器、core-decorators 库

```js
/**
* ES7 装饰器语法
**/
function testDec(isDec) {
    return function(target) {   // 最后一定是返回一个函数的
        target.isDec = isDec;
    }
}

@testDec(true)
class Demo {

}

console.log(Demo.isDec);    // true

/**
* mixins 举例
* 就是将其他对象的所有方法和属性，赋值到该类上
**/
function mixins(...list) {
    return function(target) {
        return Object.assign(target.prototype, ...list);
    }
}

const Foo = {
    foo() {
        alert('foo');
    }
}

@minxins(Foo)
class MyClass {

}

let obj = new MyClass();
obj.foo();
```

## 5 代理模式

```js
class RealImg {
    constructor(fileName) {
        this.fileName = fileName;
        this.loadFromDisk();        // 模拟初始化从硬盘读入
    }

    display() {
        console.log('display... ' + this.fileName);
    }

    loadFromDisk() {
        console.log('loading... ' + this.fileName);
    }
}

class ProxyImg {
    constructor(fileName) {
        this.realImg = new RealImg(fileName);
    }
    display() {
        this.realImg.display();
    }
}
```

场景：网页事件代理、jQuery $.proxy 、ES6 Proxy

```js
const star = {
    name: 'Kiyonami',
    age: 25,
    phone: 'star: 123456',
};

const agent = new Proxy(star, {
    get: function(target, key) {        // target 指向 star
        if(key === 'phone') {
            return 'agent: 666666';            // 经纪人不可能真的把明星的电话直接给出来吧。给经纪人自己的。
        }
        if(key === 'price') {
            return 120000;              // 明星不谈价格，经纪人来谈。
        }
        return target[key];             // 其他情况就直接返回了。（比如你想知道该明星的名字和年龄）
    },
    set: function(target, key, value) {
        if(key === 'customPrice') {     // 想来和经济人谈价格
            if(val < 100000) {
                throw new Error('价格太低');    // 经纪人的反馈
            } else {
                target[key] = value;
                return true;           // !!! 必须 return true 不然不会赋值
            }
        }
    }
});

console.log(agent.name);
console.log(agent.age);
console.log(agent.phone);
console.log(agent.price);
agent.customPrice = 130000;
```

- 适配器模式：提供一个不同的接口；代理模式：提供一模一样的接口。
- 装饰器模式：扩展功能，原有功能不变且可以直接使用；代理模式：显示原有功能，但是经过限制或者阉割。

## 6 外观模式

为子系统中的一组接口提供了一个高层接口，使用者使用这个高层接口。（其实不太符合接口独立原则（集合了），谨慎使用）

场景：
```js
// 相当于高层接口
function bindEvent(elem, type, selector, fn) {
    // 兼容 4 个参数，或者 3 个参数
    if(fn == null) {
        fn = selector;
        selector = null;
    } 
    // ...
}

// 调用两个子函数
bindEvent(elem, 'click', "#div1", fn);
bindEvent(elem, 'click', fn);
```

## 7 观察者模式

发布&订阅；一对多


![js设计模式-观察者模式](https://raw.githubusercontent.com/514723273/.md-Pictures/master/js设计模式-观察者模式.png)

```js
class Subject {
    constructor() {
        this.state = 0;
        this.observers = [];
    }
    getState() {
        return this.state;
    }
    setState(state) {
        this.state = state;
        this.notifyAllObservers();      // {!!!} 关键点！就是在主题状态变化的时候，通知所有观察者改变
    }
    notifyAllObservers() {              // 遍历通知
        this.observers.forEach(observer => {
            observer.update();
        })
    }
    register(observer) {                // 注册
        this.observers.push(observer);
    }
}

class Observer {
    constructor(testName, subject) {
        this.testName = testName;
        this.subject = subject;
        this.subject.register(this);    // 内部注册
    }

    update() {      // 所有的观察者必须有 update 方法（接口
        console.log(`${this.testName} update: subject state is ${this.subject.state}`);
    }
}

const subject = new Subject();

const observer1 = new Observer('111', subject);
const observer2 = new Observer('222', subject);
const observer3 = new Observer('333', subject);

subject.setState(1);
```

场景：网页事件绑定、Promise、jQuery cllbacks、nodejs 自定义事件、nodejs 处理 http 请求、Vue 和 React 组件生命周期触发

## 8 迭代器模式

顺序访问一个集合；使用者无需知道集合的内部结构（封装）；

```js
class Iterator {
    constructor(container) {
        this.list = container.list;
        this.index = 0;
    }
    next() {
        if(!this.hasNext()) {
            return null;
        }
        return this.list[this.index ++];
    }
    hasNext() {
        return this.index < this.list.length;
    }
}

class Container {
    constructor(list) {
        this.list = list;
    }
    getIterator() {
        return new Iterator(this);
    }
}


// test
const array = [1, 2, 3, 4, 5, 6];

const container = new Container(array);
const iterator = container.getIterator();
while(iterator.hasNext()) {
    console.log(iterator.next());
}
```

场景：ES6迭代器（`data[Symbol.Iterator]()` 返回迭代器）、jQuery each

## 9 状态模式

一个对象有状态变化；每个状态变化都会触发一个逻辑；不能总用 if...else 来控制

```js
class State {
    constructor(color) {
        this.color = color;
    }

    handle(context) {           // {!!!} 反转
        context.setState(this);
    }
}

class Context {
    constructor() {
        this.state = null;
    }

    getState() {
        return this.state;
    }
    
    setState(state) {
        this.state = state;
        console.log(`turn to ${this.state.color}`);
    }
}

//test

// 抽象的主体
const context = new Context();
// 红灯
const redState = new State('red');
// 黄灯
const yellowState = new State('yellow');
// 绿灯
const greeenState = new State('green');

// 变红
redState.handle(context);
// 变黄
yellowState.handle(context);
// 变绿
greeenState.handle(context);
```

感觉：一种控制反转，由状态去控制主体。

场景：有限状态机、简单的 Promise

## 10 原型模式

创建一个对象开销太大；clone 自己，生成一个新对象。

```js
const prototype = {
    getName: function() {
        return `${this.first} ${this.last}`;
    }
}

// 创建 x
const x = Object.create(prototype);     // !
x.first = 'A';
x.last = 'B';
alert(x.getName());

// 创建 y
const y = Object.create(prototype);     // !
y.first = 'C';
y.last = 'D';
alert(y.getName());
```

## 11 桥接模式

用于把抽象化与实现化解耦；使得二者可以独立变化

![js设计模式-桥接模式示例1](https://raw.githubusercontent.com/514723273/.md-Pictures/master/js设计模式-桥接模式示例1.png)

```js
/**
普通画图
这样每次画一种图案就要写一个单独的函数，不够灵活。
**/
class ColorShape {
    yellowCircle() {
        console.log('yellow circle');
    }
    redCircle() {
        console.log('red circle');
    }

    yellowTriangle() {
        console.log('yellow triangle');
    }
    redTriangle() {
        console.log('red triangle');
    }
}

// test
let cs = new ColorShape();
cs.yellowCircle();
cs.redCircle();
cs.yellowTriangle();
cs.redTriangle();
```

![js设计模式-桥接模式示例2](https://raw.githubusercontent.com/514723273/.md-Pictures/master/js设计模式-桥接模式示例2.png)

```js
class Color {
    constructor(name) {
        this.name = name;
    }
}

class Shape {
    constructor(name, color) {
        this.name = name;
        this.color = color;
    }

    draw() {
        console.log(`${this.color.name} ${this.name}`);
    }
}

// test
let red = new Color('red');
let yellow = new Color('yellow');

let circle = new Shape('circle', red);
circle.draw();
let triangle = new Shape('triangle', yellow);
tiangele.draw();
```

## 11 策略模式

```js
/**
* 没有使用 策略模式 版本
**/
class User {
    constructor(type) {
        this.type = type;
    }

    buy() {
        // 使用了大量的 if...else 逻辑
        if(this.type === 'ordinary') {
            console.log('普通用户购买');
        } else if(this.type === 'member') {
            console.log('会员用户购买');
        } else if(this.type === 'svip') {
            console.log('svip 用户购买');
        }
    }
}

// test
const user1 = new User('ordinary');
user1.buy();

const user2 = new User('member');
user2.buy();

const user3 = new User('svip');
user3.buy();
```

```js
/**
* 使用 策略模式 版本
* 拆出所有 if...else 逻辑，分摊到各自的类中，分别处理
**/
class OrdinaryUser {
    buy() {
        console.log('普通用户购买');
    }
}

class MemberUser {
    buy() {
        console.log('会员用户购买');
    }
}

class SvipUser {
    buy() {
        console.log('svip 用户购买');
    }
}

// test
const user1 = new OrdinaryUser();
user1.buy();

const user2 = new MemberUser();
user2.buy();

const user3 = new SvipUser();
user3.buy();
```

## 12 模板方法模式

```js
class Action {
    handle() {
        handle1();
        handle2();
        handle3();
    }
    handle1() {
        console.log(1);
    }
    handle2() {
        console.log(2);
    }
    handle3() {
        console.log(3);
    }
}
```

## 13 责任链模式

```js
class Action {
    constructor(name) {
        this.name = name;
        this.nextAction = null;
    }

    setNextAction(action) {
        this.nextAction = action;
    }

    handle() {
        console.log(`${this.name} 审批该请假申请`);
        if(this.next != null) {
            this.nextAction.handle();
        }
    }
}

const a1 = new Action('组长');
const a2 = new Action('经理');
const a3 = new Action('总监');

a1.setNextAction(a2);
a2.setNextAction(a3);

a1.handle();
```

## 14 命令模式

```js
class Receiver {
    exec() {
        console.log('执行');
    }
}

class Command {
    constructor(receiver) {
        this.receiver = receiver;
    }
    cmd() {
        console.log('触发命令');
        this.receiver.exec();
    }
}

class Invoker {
    constructor(command) {
        this.command = command;
    }
    invoke() {
        console.log('开始');
        this.command.cmd();
    }
}

// 士兵
const soldier = new Recceiver();
// 小号手
const trumpeter = new Command(soldier);
// 将军
const general = new Invoker(trumpeter);
general.invoke();

// 感觉和责任链有点像，链式调用
```

## 15 备忘录模式

```js
// 备忘录
class Memento {
    constructor(content) {
        this.content = content;
    }
}

// 备忘录列表
class CareTaker {
    constructor() {
        this.list = [];
    }
    add(memento) {
        this.list.push(memento);
    }
    get(index) {
        return this.list[index];
    }
}

// 编辑器
class Editor {
    constructor() {
        this.content = null;
    }
    setContent(content) {
        this.content = content;
    }
    getContent() {
        return this.content;
    }
    saveContentToMemento() {
        return new Memento(this.content);
    }
    getContentFromMemento(memento) {
        this.content = memento.content;
    }
}

// test
const editor = new Editor();
const careTaker = new CareTaker();

editor.setContent('111');
editor.setContent('222');
careTaker.add(editor.saveContentToMemento());   // 将当前内容备份
editor.setContent('333');
careTaker.add(editor.saveContentToMemento());   // 将当前内容备份
editor.setContent('444');

console.log(editor.getContent());

editor.getContentFromMemento(careTaker.get(1));
console.log(editor.getContent());

editor.getContentFromMemento(careTaker.get(0));
console.log(editor.getContent());

```

## 16 中介者模式

![js设计模式-中介者模式概念](https://raw.githubusercontent.com/514723273/.md-Pictures/master/js设计模式-中介者模式概念.png)

```js
class Tenant {
    constructor() {
        this.money = 0;   
    }
    setMoney(money, mediator) {
        this.money = money;     // 预计支出
        if(mediator != null) {
            mediator.tellLandlordHowMuchToGet();    // {!!!} 是这边调用 mediator
        }
    }
}

class Landlord {
    constructor() {
        this.money = 0;    
    }
    setMoney(money, mediator) {
        this.money = money;     // 期望利润
        if(mediator != null) {
            mediator.tellTenantHowMuchToPay();
        }
    }
}

class Mediator {
    constructor(tenant, landlord) {
        this.tenant = tenant;     // 美 [ˈtenənt] 租客
        this.landlord = landlord;     // 房东
    }
    // 告诉租客需要付多少钱
    tellTenantHowMuchToPay() {
        const money = this.landlord.money;    // 房东期望利润
        this.tenant.setMoney(money / 0.1);     // 租客需付这么多钱；房东的利润占 10%
    }

    // 告诉房东能挣多少钱
    tellLandlordHowMuchToGet() {
        const money = this.tenant.money;     // 租客可以付的钱
        this.landlord.setMoney(money * 0.1);    // 房东可以挣这么多钱；
    }
}

const tenant = new Tenant();
const landlord = new Landlord();
const mediator = new Mediator(tenant, landlord);

tenant.setMoney(2000, mediator);
console.log(tenant.money, landlord.money);

landlord.setMoney(300, mediator);
console.log(tenant.money, landlord.money);

```