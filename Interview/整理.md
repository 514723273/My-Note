# 1 原型、原型链

## 1.1 概念

原型：每一个 JavaScript 对象（ null 除外）在创建的时候，就会和另一个对象相关联。这个对象就是我们所说的原型，每一个对象都会从原型"继承"属性。

原型链：是一种机制，指的是 JavaScript 每个对象（包括原型对象）都有一个内置的`__proto__`属性指向创建它的函数对象的原型对象（即 prototype 属性）。原型链的存在主要是为了实现对象的继承。

## 1.2 总结

- 函数拥有显式原型（`prototype`）
- 对象拥有隐式原型（`__proto__`)
- 原型是一个普通的对象，所以依旧有隐式原型（`__proto__`），指向它的构造函数的原型（`prototype`）

![原型链1](https://raw.githubusercontent.com/514723273/.md-Pictures/master/原型链1.png)

# 2 作用域

## 2.1 概念

作用域：是指程序源代码中定义变量的区域。确定当前执行代码对变量的访问权限。

因为 JavaScript 采用的是*词法作用域*，**函数的作用域在函数定义的时候就决定了。**

# 3 继承

## 3.1 组合继承

```js
function Parent(age, name) {
    this.age = age;
    this.name = name;
}

Parent.prototype.getName = function() {
    return this.name;
}

function Child(age, name, job="engineer") {
    Parent.call(this, age, name);       // 相当于在这个函数内执行了 this.age = age; this.name = name； // 并没有单独调用 Parent() 此时没有形成原型链!!!
    this.job = job;
}

Child.prototype.__proto__ === Object.prototype  // true

// let child1 = new Child(14, "Kiyonami", "singer");   // 此时的 child1.__proto__ 等于 Object.prototype

//!!!
Child.prototype = new Parent();         // 这边才是继承，此时才形成了原型链!!!  // 因为调用了 new Parent() 所以 child2.__proto__ 多了 age name 属性    // Child.prototype.__proto__ = new Parent(); 这样写是将函数的原型继承了 Parent

let child2 = new Child(14, "Kiyonami", "singer");

console.log(child2.getName());
```

## 3.2 寄生组合继承

```js
function Parent(age, name) {
    this.age = age;
    this.name = name;
}

function Child(age, name, job) {
    Parent.call(this, age, name);
    this.job = job;
}

//!!!
Child.prototype = Object.create(Parent.prototype, {     // 与 Child.prototype = new Parent(); 差别就是，没有调用构造函数，而是直接拿原型赋值；Parent 原型内没有 age name 属性，只有方法，且是对象，有__proto__指向其父原型 Object 。这样就不会有重复属性了。
    constructor: {
        value: Child,
        enumerable: false,
        writable: true,
        configurable: true,
    }
});

let child = new Child(14, "Kiyonami", "engineer");

```