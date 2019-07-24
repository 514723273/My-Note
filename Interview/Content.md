摘录自 [前端面试之道](https://juejin.im/book/5bdc715fe51d454e755f75ef)，方便查阅。

# 1 JS 基础知识点及常考面试题（一）

JS 对于每位前端开发都是必备技能，在小册中我们也会有多个章节去讲述这部分的知识。首先我们先来熟悉下 JS 的一些常考和容易混乱的基础知识点。

## 1.1 原始（Primitive）类型

```!
涉及面试题：原始类型有哪几种？null 是对象嘛？
```

在 JS 中，存在着 6 种原始值，分别是：

- `boolean`
- `null`
- `undefined`
- `number`
- `string`
- `symbol`

首先原始类型存储的都是值，是没有函数可以调用的，比如 `undefined.toString()`

![](https://user-gold-cdn.xitu.io/2018/11/14/16711c4f991c73ac?w=526&h=53&f=png)

此时你肯定会有疑问，这不对呀，明明 `'1'.toString()` 是可以使用的。其实在这种情况下，`'1'` 已经不是原始类型了，而是被强制转换成了 `String` 类型也就是对象类型，所以可以调用 `toString` 函数。

除了会在必要的情况下强转类型以外，原始类型还有一些坑。

其中 JS 的 `number` 类型是浮点类型的，在使用中会遇到某些 Bug，比如 `0.1 + 0.2 !== 0.3`，但是这一块的内容会在进阶部分讲到。`string` 类型是不可变的，无论你在 `string` 类型上调用何种方法，都不会对值有改变。

另外对于 `null` 来说，很多人会认为他是个对象类型，其实这是错误的。虽然 `typeof null` 会输出 `object`，但是这只是 JS 存在的一个悠久 Bug。在 JS 的最初版本中使用的是 32 位系统，为了性能考虑使用低位存储变量的类型信息，`000` 开头代表是对象，然而 `null` 表示为全零，所以将它错误的判断为 `object` 。虽然现在的内部类型判断代码已经改变了，但是对于这个 Bug 却是一直流传下来。

## 1.2 对象（Object）类型

```!
涉及面试题：对象类型和原始类型的不同之处？函数参数是对象会发生什么问题？
```

在 JS 中，除了原始类型那么其他的都是对象类型了。对象类型和原始类型不同的是，原始类型存储的是值，对象类型存储的是地址（指针）。当你创建了一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但是我们需要找到这个空间，这个空间会拥有一个地址（指针）。

```js
const a = []
```

对于常量 `a` 来说，假设内存地址（指针）为 `#001`，那么在地址 `#001` 的位置存放了值 `[]`，常量 `a` 存放了地址（指针） `#001`，再看以下代码

```js
const a = []
const b = a
b.push(1)
```

当我们将变量赋值给另外一个变量时，复制的是原本变量的地址（指针），也就是说当前变量 `b` 存放的地址（指针）也是 `#001`，当我们进行数据修改的时候，就会修改存放在地址（指针） `#001` 上的值，也就导致了两个变量的值都发生了改变。

接下来我们来看函数参数是对象的情况

```js
function test(person) {
  person.age = 26
  person = {
    name: 'yyy',
    age: 30
  }

  return person
}
const p1 = {
  name: 'yck',
  age: 25
}
const p2 = test(p1)
console.log(p1) // -> ?
console.log(p2) // -> ?
```

对于以上代码，你是否能正确的写出结果呢？接下来让我为你解析一番：
- 首先，函数传参是传递对象指针的副本
- 到函数内部修改参数的属性这步，我相信大家都知道，当前 `p1` 的值也被修改了
- 但是当我们重新为 `person` 分配了一个对象时就出现了分歧，请看下图

![](https://user-gold-cdn.xitu.io/2018/11/14/16712ce155afef8c?w=658&h=414&f=png)

所以最后 `person` 拥有了一个新的地址（指针），也就和 `p1` 没有任何关系了，导致了最终两个变量的值是不相同的。

## 1.3 typeof vs instanceof

```!
涉及面试题：typeof 是否能正确判断类型？instanceof 能正确判断对象的原理是什么？
```

`typeof` 对于原始类型来说，除了 `null` 都可以显示正确的类型

```js
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
```

`typeof` 对于对象来说，除了函数都会显示 `object`，所以说 `typeof` 并不能准确判断变量到底是什么类型

```js
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```

如果我们想判断一个对象的正确类型，这时候可以考虑使用 `instanceof`，因为内部机制是通过原型链来判断的，在后面的章节中我们也会自己去实现一个 `instanceof`。

```js
const Person = function() {}
const p1 = new Person()
p1 instanceof Person // true

var str = 'hello world'
str instanceof String // false

var str1 = new String('hello world')
str1 instanceof String // true
```

对于原始类型来说，你想直接通过 `instanceof` 来判断类型是不行的，当然我们还是有办法让 `instanceof` 判断原始类型的

```js
class PrimitiveString {
  static [Symbol.hasInstance](x) {
    return typeof x === 'string'
  }
}
console.log('hello world' instanceof PrimitiveString) // true
```

你可能不知道 `Symbol.hasInstance` 是什么东西，其实就是一个能让我们自定义 `instanceof` 行为的东西，以上代码等同于 `typeof 'hello world' === 'string'`，所以结果自然是 `true` 了。这其实也侧面反映了一个问题， `instanceof` 也不是百分之百可信的。

## 1.4 类型转换

```!
涉及面试题：该知识点常在笔试题中见到，熟悉了转换规则就不惧怕此类题目了。
```

首先我们要知道，在 JS 中类型转换只有三种情况，分别是：
- 转换为布尔值
- 转换为数字
- 转换为字符串

我们先来看一个类型转换表格，然后再进入正题

### 1.4.1 原始值转换为 boolean 值

|原始值|结果|
| --- | --- |
| symbol | true |
| undefined | false |
| null | false |
| ~~boolean~~ | 无需转换 |
| number | 0, -1, NaN 都为 false / 其余为 true |
| string | 空字符串 为 false / 其余为 true |
| 引用类型 | true |

### 1.4.2 原始类型转换为 string

|原始值|结果|
| --- | --- |
| symbol | 出错 |
| undefined | 'undefined' |
| null | 'null' |
| boolean | true => 'true' / false => 'false' |
| number | 1 => '1' -1 => '-1' |
| ~~string~~ | 无需转换 |
| 引用类型 | 数组 [1, 2] => '1, 2' / 对象 '[object Object]' |

### 1.4.3 原始类型转换为 number

|原始类型|结果|
| --- | --- |
| symbol | 出错 |
| undefined | NaN |
| null | 0 |
| boolean | true => 1 / false => 0 |
| ~~number~~ | 无需转换 |
| string | '1' => 1 / 'a' => NaN |
| 引用类型 | 空数组 => 0 / 存在一个数字元素的数组转数字 / 其他数组转 NaN / 对象转 NaN |

### 1.4.4 转Boolean

在条件判断时，除了 `undefined`， `null`， `false`， `NaN`， `''`， `0`， `-0`，其他所有值都转为 `true`，包括所有对象。

### 1.4.5 对象转原始类型

对象在转换类型的时候，会调用内置的 `[[ToPrimitive]]` 函数，对于该函数来说，算法逻辑一般来说如下：

- 如果已经是原始类型了，那就不需要转换了
- 调用 `x.valueOf()`，如果转换为基础类型，就返回转换的值
- 调用 `x.toString()`，如果转换为基础类型，就返回转换的值
- 如果都没有返回原始类型，就会报错

当然你也可以重写 `Symbol.toPrimitive` ，该方法在转原始类型时调用优先级最高。

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  },
  [Symbol.toPrimitive]() {
    return 2
  }
}
1 + a // => 3
```

### 1.4.5 四则运算符

加法运算符不同于其他几个运算符，它有以下几个特点：
- 运算中其中一方为字符串，那么就会把另一方也转换为字符串
- 如果一方不是字符串或者数字，那么会将它转换为数字或者字符串

```js
1 + '1' // '11'
true + true // 2
4 + [1,2,3] // "41,2,3"
```

如果你对于答案有疑问的话，请看解析：
- 对于第一行代码来说，触发特点一，所以将数字 `1` 转换为字符串，得到结果 `'11'`
- 对于第二行代码来说，触发特点二，所以将 `true` 转为数字 `1`
- 对于第三行代码来说，触发特点二，所以将数组通过 `toString` 转为字符串 `1,2,3`，得到结果 `41,2,3`

另外对于加法还需要注意这个表达式 `'a' + + 'b'`

```js
'a' + + 'b' // -> "aNaN"
```

因为 `+ 'b'` 等于 `NaN`，所以结果为 `"aNaN"`，你可能也会在一些代码中看到过 `+ '1'` 的形式来快速获取 `number` 类型。

那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字

```js
4 * '3' // 12
4 * [] // 0
4 * [1, 2] // NaN
```

### 1.4.6 比较运算符

1. 如果是对象，就通过 `toPrimitive` 转换对象
2. 如果是字符串，就通过 `unicode` 字符索引来比较

```js
let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  }
}
a > -1 // true
```

在以上代码中，因为 `a` 是对象，所以会通过 `valueOf` 转换为原始类型再比较值。

## 1.5 this

```!
涉及面试题：如何正确判断 this？箭头函数的 this 是什么？
```

`this` 是很多人会混淆的概念，但是其实它一点都不难，只是网上很多文章把简单的东西说复杂了。在这一小节中，你一定会彻底明白 `this` 这个概念的。

我们先来看几个函数调用的场景
```js
function foo() {
  console.log(this.a)
}
var a = 1
foo()

const obj = {
  a: 2,
  foo: foo
}
obj.foo()

const c = new foo()
```

接下来我们一个个分析上面几个场景
- 对于直接调用 `foo` 来说，不管 `foo` 函数被放在了什么地方，`this` 一定是 `window`
- 对于 `obj.foo()` 来说，我们只需要记住，谁调用了函数，谁就是 `this`，所以在这个场景下 `foo` 函数中的 `this` 就是 `obj` 对象
- 对于 `new` 的方式来说，`this` 被永远绑定在了 `c` 上面，不会被任何方式改变 `this`

说完了以上几种情况，其实很多代码中的 `this` 应该就没什么问题了，下面让我们看看箭头函数中的 `this`

```js
function a() {
  return () => {
    return () => {
      console.log(this)
    }
  }
}
console.log(a()()())
```

首先箭头函数其实是没有 `this` 的，箭头函数中的 `this` 只取决包裹箭头函数的第一个普通函数的 `this`。在这个例子中，因为包裹箭头函数的第一个普通函数是 `a`，所以此时的 `this` 是 `window`。另外对箭头函数使用 `bind` 这类函数是无效的。

最后种情况也就是 `bind` 这些改变上下文的 API 了，对于这些函数来说，`this` 取决于第一个参数，如果第一个参数为空，那么就是 `window`。

那么说到 `bind`，不知道大家是否考虑过，如果对一个函数进行多次 `bind`，那么上下文会是什么呢？

```js
let a = {}
let fn = function () { console.log(this) }
fn.bind().bind(a)() // => ?
```

如果你认为输出结果是 `a`，那么你就错了，其实我们可以把上述代码转换成另一种形式

```js
// fn.bind().bind(a) 等于
let fn2 = function fn1() {
  return function() {
    return fn.apply()
  }.apply(a)
}
fn2()
```

可以从上述代码中发现，不管我们给函数 `bind` 几次，`fn` 中的 `this` 永远由第一次 `bind` 决定，所以结果永远是 `window`。

```js
let a = { name: 'yck' }
function foo() {
  console.log(this.name)
}
foo.bind(a)() // => 'yck'
```

以上就是 `this` 的规则了，但是可能会发生多个规则同时出现的情况，这时候不同的规则之间会根据优先级最高的来决定 `this` 最终指向哪里。

首先，`new` 的方式优先级最高，接下来是 `bind` 这些函数，然后是 `obj.foo()` 这种调用方式，最后是 `foo` 这种调用方式，同时，箭头函数的 `this` 一旦被绑定，就不会再被任何方式所改变。

如果你还是觉得有点绕，那么就看以下的这张流程图吧，图中的流程只针对于单个规则。

![](https://user-gold-cdn.xitu.io/2018/11/15/16717eaf3383aae8?w=744&h=531&f=png)

## 1.6 == vs ===

```!
涉及面试题：== 和 === 有什么区别？
```

对于 `==` 来说，如果对比双方的类型**不一样**的话，就会进行**类型转换**，这也就用到了我们上一章节讲的内容。

假如我们需要对比 `x` 和 `y` 是否相同，就会进行如下判断流程：

1. 首先会判断两者类型是否**相同**。相同的话就是比大小了
2. 类型不相同的话，那么就会进行类型转换
3. 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
4. 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`
    ```js
    1 == '1'
          ↓
    1 ==  1
    ```
5. 判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断
    ```js
    '1' == true
            ↓
    '1' ==  1
            ↓
     1  ==  1
    ```
6. 判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断
    ```js
    '1' == { name: 'yck' }
            ↓
    '1' == '[object Object]'
    ```

```!
思考题：看完了上面的步骤，对于 [] == ![] 你是否能正确写出答案呢？
```

如果你觉得记忆步骤太麻烦的话，我还提供了流程图供大家使用：

![](https://user-gold-cdn.xitu.io/2018/12/19/167c4a2627fe55f1?w=1005&h=426&f=png&s=38534)

当然了，这个流程图并没有将所有的情况都列举出来，我这里只将常用到的情况列举了，如果你想了解更多的内容可以参考 [标准文档](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.1)。

对于 `===` 来说就简单多了，就是判断两者类型和值是否相同。

## 1.7 闭包

```!
涉及面试题：什么是闭包？
```

闭包的定义其实很简单：函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。

```js
function A() {
  let a = 1
  window.B = function () {
      console.log(a)
  }
}
A()
B() // 1
```

很多人对于闭包的解释可能是函数嵌套了函数，然后返回一个函数。其实这个解释是不完整的，就比如我上面这个例子就可以反驳这个观点。

在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。

```!
经典面试题，循环中使用闭包解决 `var` 定义函数的问题
```

```Js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

首先因为 `setTimeout` 是个异步函数，所以会先把循环全部执行完毕，这时候 `i` 就是 6 了，所以会输出一堆 6。

解决办法有三种，第一种是使用闭包的方式

```js
for (var i = 1; i <= 5; i++) {
  ;(function(j) {
    setTimeout(function timer() {
      console.log(j)
    }, j * 1000)
  })(i)
}
```

在上述代码中，我们首先使用了立即执行函数将 `i` 传入函数内部，这个时候值就被固定在了参数 `j` 上面不会改变，当下次执行 `timer` 这个闭包的时候，就可以使用外部函数的变量 `j`，从而达到目的。

第二种就是使用 `setTimeout ` 的第三个参数，这个参数会被当成 `timer` 函数的参数传入。

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(
    function timer(j) {
      console.log(j)
    },
    i * 1000,
    i
  )
}
```

第三种就是使用 `let` 定义 `i` 了来解决问题了，这个也是最为推荐的方式

```js
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```

## 1.8 深浅拷贝

```!
涉及面试题：什么是浅拷贝？如何实现浅拷贝？什么是深拷贝？如何实现深拷贝？
```

在上一章节中，我们了解了对象类型在赋值的过程中其实是复制了地址，从而会导致改变了一方其他也都被改变的情况。通常在开发中我们不希望出现这样的问题，我们可以使用浅拷贝来解决这个情况。

```js
let a = {
  age: 1
}
let b = a
a.age = 2
console.log(b.age) // 2
```

### 1.8.1 浅拷贝

首先可以通过 `Object.assign` 来解决这个问题，很多人认为这个函数是用来深拷贝的。其实并不是，`Object.assign` 只会拷贝所有的属性值到新的对象中，*如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝。*

```js
let a = {
  age: 1
}
let b = Object.assign({}, a)
a.age = 2
console.log(b.age) // 1
```

另外我们还可以通过展开运算符 `...` 来实现浅拷贝

```js
let a = {
  age: 1
}
let b = { ...a }
a.age = 2
console.log(b.age) // 1
```

通常浅拷贝就能解决大部分问题了，但是当我们遇到如下情况就可能需要使用到深拷贝了

```js
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = { ...a }
a.jobs.first = 'native'
console.log(b.jobs.first) // native
```

浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到最开始的话题了，两者享有相同的地址。要解决这个问题，我们就得使用深拷贝了。

### 1.8.2 深拷贝

这个问题通常可以通过 `JSON.parse(JSON.stringify(object))` 来解决。

```js
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE
```

但是该方法也是有局限性的：

- 会忽略 `undefined`
- 会忽略 `symbol`
- 不能序列化函数
- 不能解决循环引用的对象

```js
let obj = {
  a: 1,
  b: {
    c: 2,
    d: 3,
  },
}
obj.c = obj.b
obj.e = obj.a
obj.b.c = obj.c
obj.b.d = obj.b
obj.b.e = obj.b.c
let newObj = JSON.parse(JSON.stringify(obj))
console.log(newObj)
```

如果你有这么一个循环引用对象，你会发现并不能通过该方法实现深拷贝

![](https://user-gold-cdn.xitu.io/2018/3/28/1626b1ec2d3f9e41?w=840&h=100&f=png&s=30123)

在遇到函数、 `undefined` 或者 `symbol` 的时候，该对象也不能正常的序列化

```js
let a = {
  age: undefined,
  sex: Symbol('male'),
  jobs: function() {},
  name: 'yck'
}
let b = JSON.parse(JSON.stringify(a))
console.log(b) // {name: "yck"}
```

你会发现在上述情况中，该方法会忽略掉函数和 `undefined` 。

但是在通常情况下，复杂数据都是可以序列化的，所以这个函数可以解决大部分问题。

如果你所需拷贝的对象含有内置类型并且不包含函数，可以使用 `MessageChannel`

```js
function structuralClone(obj) {
  return new Promise(resolve => {
    const { port1, port2 } = new MessageChannel()
    port2.onmessage = ev => resolve(ev.data)
    port1.postMessage(obj)
  })
}

var obj = {
  a: 1,
  b: {
    c: 2
  }
}

obj.b.d = obj.b

// 注意该方法是异步的
// 可以处理 undefined 和循环引用对象
const test = async () => {
  const clone = await structuralClone(obj)
  console.log(clone)
}
test()
```

当然你可能想自己来实现一个深拷贝，但是其实实现一个深拷贝是很困难的，需要我们考虑好多种边界情况，比如原型链如何处理、DOM 如何处理等等，所以这里我们实现的深拷贝只是简易版，并且我其实更推荐使用 [lodash 的深拷贝函数](https://lodash.com/docs#cloneDeep)。

```js
function deepClone(obj) {
  function isObject(o) {
    return (typeof o === 'object' || typeof o === 'function') && o !== null
  }

  if (!isObject(obj)) {
    throw new Error('非对象')
  }

  let isArray = Array.isArray(obj)
  let newObj = isArray ? [...obj] : { ...obj }
  Reflect.ownKeys(newObj).forEach(key => {
    newObj[key] = isObject(obj[key]) ? deepClone(obj[key]) : obj[key]
  })

  return newObj
}

let obj = {
  a: [1, 2, 3],
  b: {
    c: 2,
    d: 3
  }
}
let newObj = deepClone(obj)
newObj.b.c = 1
console.log(obj.b.c) // 2
```

## 1.9 原型

```!
涉及面试题：如何理解原型？如何理解原型链？
```

当我们创建一个对象时 `let obj = { age: 25 }`，我们可以发现能使用很多种函数，但是我们明明没有定义过它们，对于这种情况你是否有过疑惑？

![](https://user-gold-cdn.xitu.io/2018/11/16/1671d15f45fcedea?w=245****&h=73&f=png&s=8860)

当我们在浏览器中打印 `obj` 时你会发现，在 `obj` 上居然还有一个 `__proto__` 属性，那么看来之前的疑问就和这个属性有关系了。

其实每个 JS 对象都有 `__proto__` 属性，这个属性指向了原型。这个属性在现在来说已经不推荐直接去使用它了，这只是浏览器在早期为了让我们访问到内部属性 `[[prototype]]` 来实现的一个东西。

讲到这里好像还是没有弄明白什么是原型，接下来让我们再看看 `__proto__` 里面有什么吧。

![](https://user-gold-cdn.xitu.io/2018/11/16/1671d2c5a6bcccc4?w=383&h=271&f=png&s=41376)

看到这里你应该明白了，原型也是一个对象，并且这个对象中包含了很多函数，所以我们可以得出一个结论：对于 `obj` 来说，可以通过 `__proto__` 找到一个原型对象，在该对象中定义了很多函数让我们来使用。

在上面的图中我们还可以发现一个 `constructor` 属性，也就是构造函数

![](https://user-gold-cdn.xitu.io/2018/11/16/1671d329ec98ec0b?w=581&h=404&f=png&s=82731)

打开 `constructor` 属性我们又可以发现其中还有一个 `prototype` 属性，并且这个属性对应的值和先前我们在 `__proto__` 中看到的一模一样。所以我们又可以得出一个结论：原型的 `constructor` 属性指向构造函数，构造函数又通过 `prototype` 属性指回原型，但是并不是所有函数都具有这个属性，`Function.prototype.bind()` 就没有这个属性。

其实原型就是那么简单，接下来我们再来看一张图，相信这张图能让你彻底明白原型和原型链

![](https://user-gold-cdn.xitu.io/2018/11/16/1671d387e4189ec8?w=618&h=781&f=png&s=266099)

看完这张图，我再来解释下什么是原型链吧。其实原型链就是多个对象通过 `__proto__` 的方式连接了起来。为什么 `obj` 可以访问到 `valueOf` 函数，就是因为 `obj` 通过原型链找到了 `valueOf` 函数。

对于这一小节的知识点，总结起来就是以下几点：
- `Object` 是所有对象的爸爸，所有对象都可以通过 `__proto__` 找到它
- `Function` 是所有函数的爸爸，所有函数都可以通过 `__proto__` 找到它
- 函数的 `prototype` 是一个对象
- 对象的 `__proto__` 属性指向原型， `__proto__` 将对象和原型连接起来组成了原型链

# 2 ES6 知识点及常考面试题

本章节我们将来学习 ES6 部分的内容。

## 2.1 var、let 及 const 区别

```!
涉及面试题：什么是提升？什么是暂时性死区？var、let 及 const 区别？
```

对于这个问题，我们应该先来了解提升（hoisting）这个概念。

```js
console.log(a) // undefined
var a = 1
```

从上述代码中我们可以发现，虽然变量还没有被声明，但是我们却可以使用这个未被声明的变量，这种情况就叫做提升，并且提升的是声明。

对于这种情况，我们可以把代码这样来看

```js
var a
console.log(a) // undefined
a = 1
```

接下来我们再来看一个例子

```js
var a = 10
var a
console.log(a)
```

对于这个例子，如果你认为打印的值为 `undefined` 那么就错了，答案应该是 `10`，对于这种情况，我们这样来看代码

```js
var a
var a
a = 10
console.log(a)
```

到这里为止，我们已经了解了 `var` 声明的变量会发生提升的情况，其实不仅变量会提升函数也会被提升。

```js
console.log(a) // ƒ a() {}
function a() {}
var a = 1
```

对于上述代码，打印结果会是 `ƒ a() {}`，即使变量声明在函数之后，这也说明了函数会被提升，并且优先于变量提升。

说完了这些，想必大家也知道 `var` 存在的问题了，使用 `var` 声明的变量会被提升到作用域的顶部，接下来我们再来看 `let` 和 `const` 。

我们先来看一个例子：

```js
var a = 1
let b = 1
const c = 1
console.log(window.b) // undefined
console.log(window. c) // undefined

function test(){
  console.log(a)
  let a
}
test()
```

首先在全局作用域下使用 `let` 和 `const` 声明变量，变量并不会被挂载到 `window` 上，这一点就和 `var` 声明有了区别。

再者当我们在声明 `a` 之前如果使用了 `a`，就会出现报错的情况

![](https://user-gold-cdn.xitu.io/2018/11/18/1672730318cfa540?w=447&h=49&f=png&s=10542)

你可能会认为这里也出现了提升的情况，但是因为某些原因导致不能访问。

首先报错的原因是因为存在暂时性死区，我们不能在声明前就使用变量，这也是 `let` 和 `const` 优于 `var` 的一点。然后这里你认为的提升和 `var` 的提升是有区别的，虽然变量在编译的环节中被告知在这块作用域中可以访问，但是访问是受限制的。

那么到这里，想必大家也都明白 `var`、`let` 及 `const` 区别了，不知道你是否会有这么一个疑问，为什么要存在提升这个事情呢，其实提升存在的根本原因就是为了解决函数间互相调用的情况

```js
function test1() {
    test2()
}
function test2() {
    test1()
}
test1()
```

假如不存在提升这个情况，那么就实现不了上述的代码，因为不可能存在 `test1` 在 `test2` 前面然后 `test2` 又在 `test1` 前面。

那么最后我们总结下这小节的内容：
- 函数提升优先于变量提升，函数提升会把整个函数挪到作用域顶部，变量提升只会把声明挪到作用域顶部
- `var` 存在提升，我们能在声明之前使用。`let`、`const` 因为暂时性死区的原因，不能在声明前使用
- `var` 在全局作用域下声明变量会导致变量挂载在 `window` 上，其他两者不会
- `let` 和 `const` 作用基本一致，但是后者声明的变量不能再次赋值

## 2.2 原型继承和 Class 继承

```!
涉及面试题：原型如何实现继承？Class 如何实现继承？Class 本质是什么？
```

首先先来讲下 `class`，其实在 JS 中并不存在类，`class` 只是语法糖，本质还是函数。

```js
class Person {}
Person instanceof Function // true
```

在上一章节中我们讲解了原型的知识点，在这一小节中我们将会使用分别使用原型和 `class` 的方式来实现继承。

### 2.2.1 组合继承

组合继承是最常用的继承方式，

```js
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}
function Child(value) {
  Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

以上继承的方式核心是在子类的构造函数中通过 `Parent.call(this)` 继承父类的属性，然后改变子类的原型为 `new Parent()` 来继承父类的函数。

这种继承方式优点在于构造函数可以传参，不会与父类引用属性共享，可以复用父类的函数，但是也存在一个缺点就是在继承父类函数的时候调用了父类构造函数，导致子类的原型上多了不需要的父类属性，存在内存上的浪费。

![](https://user-gold-cdn.xitu.io/2018/11/19/1672aeb24a2e2cae?w=319&h=105&f=png&s=9858)

### 2.2.2 寄生组合继承

这种继承方式对组合继承进行了优化，组合继承缺点在于继承父类函数时调用了构造函数，我们只需要优化掉这点就行了。

```js
function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child,
    enumerable: false,
    writable: true,
    configurable: true
  }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

以上继承实现的核心就是将父类的原型赋值给了子类，并且将构造函数设置为子类，这样既解决了无用的父类属性问题，还能正确的找到子类的构造函数。

![](https://user-gold-cdn.xitu.io/2018/11/19/1672afb8dfa21361?w=358&h=101&f=png&s=11092)

### 2.2.3 Class 继承

以上两种继承方式都是通过原型去解决的，在 ES6 中，我们可以使用 `class` 去实现继承，并且实现起来很简单

```js
class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}
class Child extends Parent {
  constructor(value) {
    super(value)
    this.val = value
  }
}
let child = new Child(1)
child.getValue() // 1
child instanceof Parent // true
```

`class` 实现继承的核心在于使用 `extends` 表明继承自哪个父类，并且在子类构造函数中必须调用 `super`，因为这段代码可以看成 `Parent.call(this, value)`。

当然了，之前也说了在 JS 中并不存在类，`class` 的本质就是函数。

## 2.3 模块化

```!
涉及面试题：为什么要使用模块化？都有哪几种方式可以实现模块化，各有什么特点？
```

使用一个技术肯定是有原因的，那么使用模块化可以给我们带来以下好处
- 解决命名冲突
- 提供复用性
- 提高代码可维护性

### 2.3.1 立即执行函数

在早期，使用立即执行函数实现模块化是常见的手段，通过函数作用域解决了命名冲突、污染全局作用域的问题 

```js
(function(globalVariable){
   globalVariable.test = function() {}
   // ... 声明各种变量、函数都不会污染全局作用域
})(globalVariable)
```

### 2.3.2 AMD 和 CMD

鉴于目前这两种实现方式已经很少见到，所以不再对具体特性细聊，只需要了解这两者是如何使用的。

```js
// AMD
define(['./a', './b'], function(a, b) {
  // 加载模块完毕可以使用
  a.do()
  b.do()
})
// CMD
define(function(require, exports, module) {
  // 加载模块
  // 可以把 require 写在函数体的任意地方实现延迟加载
  var a = require('./a')
  a.doSomething()
})
```

### 2.3.3 CommonJS

CommonJS 最早是 Node 在使用，目前也仍然广泛使用，比如在 Webpack 中你就能见到它，当然目前在 Node 中的模块管理已经和 CommonJS 有一些区别了。

```js
// a.js
module.exports = {
    a: 1
}
// or 
exports.a = 1

// b.js
var module = require('./a.js')
module.a // -> log 1
```

因为 CommonJS 还是会使用到的，所以这里会对一些疑难点进行解析

先说 `require` 吧

```js
var module = require('./a.js')
module.a 
// 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，
// 重要的是 module 这里，module 是 Node 独有的一个变量
module.exports = {
    a: 1
}
// module 基本实现
var module = {
  id: 'xxxx', // 我总得知道怎么去找到他吧
  exports: {} // exports 就是个空对象
}
// 这个是为什么 exports 和 module.exports 用法相似的原因
var exports = module.exports 
var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
};
// 然后当我 require 的时候去找到独特的
// id，然后将要使用的东西用立即执行函数包装下，over
```

另外虽然 `exports` 和 `module.exports` 用法相似，但是不能对 `exports` 直接赋值。因为 `var exports = module.exports ` 这句代码表明了 `exports` 和 `module.exports` 享有相同地址，通过改变对象的属性值会对两者都起效，但是如果直接对 `exports` 赋值就会导致两者不再指向同一个内存地址，修改并不会对 `module.exports` 起效。

### 2.3.4 ES Module

ES Module 是原生实现的模块化方案，与 CommonJS 有以下几个区别

- CommonJS 支持动态导入，也就是 `require(${path}/xx.js)`，后者目前不支持，但是已有提案
- CommonJS 是同步导入，因为用于服务端，文件都在本地，同步导入即使卡住主线程影响也不大。而后者是异步导入，因为用于浏览器，需要下载文件，如果也采用同步导入会对渲染有很大影响
- CommonJS 在导出时都是值拷贝，就算导出的值变了，导入的值也不会改变，所以如果想更新值，必须重新导入一次。但是 ES Module 采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化
- ES Module 会编译成 `require/exports` 来执行的

```js
// 引入模块 API
import XXX from './a.js'
import { XXX } from './a.js'
// 导出模块 API
export function a() {}
export default function() {}
```

## 2.4 Proxy

```!
涉及面试题：Proxy 可以实现什么功能？
```

如果你平时有关注 Vue 的进展的话，可能已经知道了在 Vue3.0 中将会通过 `Proxy` 来替换原本的 `Object.defineProperty` 来实现数据响应式。
Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作。

```js
let p = new Proxy(target, handler)
```

`target` 代表需要添加代理的对象，`handler` 用来自定义对象中的操作，比如可以用来自定义 `set` 或者 `get` 函数。


接下来我们通过 `Proxy` 来实现一个数据响应式

```js
let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
      setBind(value, property)
      return Reflect.set(target, property, value)
    }
  }
  return new Proxy(obj, handler)
}

let obj = { a: 1 }
let p = onWatch(
  obj,
  (v, property) => {
    console.log(`监听到属性${property}改变为${v}`)
  },
  (target, property) => {
    console.log(`'${property}' = ${target[property]}`)
  }
)
p.a = 2 // 监听到属性a改变
p.a // 'a' = 2
```

在上述代码中，我们通过自定义 `set` 和 `get` 函数的方式，在原本的逻辑中插入了我们的函数逻辑，实现了在对对象任何属性进行读写时发出通知。

当然这是简单版的响应式实现，如果需要实现一个 Vue 中的响应式，需要我们在 `get` 中收集依赖，在 `set` 派发更新，之所以 Vue3.0 要使用 `Proxy` 替换原本的 API 原因在于 `Proxy` 无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到，但是 `Proxy` 可以完美监听到任何方式的数据改变，唯一缺陷可能就是浏览器的兼容性不好了。

## 2.5 map, filter, reduce

```!
涉及面试题：map, filter, reduce 各自有什么作用？
```

`map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中。

```js
[1, 2, 3].map(v => v + 1) // -> [2, 3, 4]
```

另外 `map` 的回调函数接受三个参数，分别是当前索引元素，索引，原数组

```js
['1','2','3'].map(parseInt)
```

- 第一轮遍历 `parseInt('1', 0) -> 1`
- 第二轮遍历 `parseInt('2', 1) -> NaN`
- 第三轮遍历 `parseInt('3', 2) -> NaN`

`filter` 的作用也是生成一个新数组，在遍历数组的时候将返回值为 `true` 的元素放入新数组，我们可以利用这个函数删除一些不需要的元素

```js
let array = [1, 2, 4, 6]
let newArray = array.filter(item => item !== 6)
console.log(newArray) // [1, 2, 4]
```

和 `map` 一样，`filter` 的回调函数也接受三个参数，用处也相同。

最后我们来讲解 `reduce` 这块的内容，同时也是最难理解的一块内容。`reduce` 可以将数组中的元素通过回调函数最终转换为一个值。

如果我们想实现一个功能将函数里的元素全部相加得到一个值，可能会这样写代码

```js
const arr = [1, 2, 3]
let total = 0
for (let i = 0; i < arr.length; i++) {
  total += arr[i]
}
console.log(total) //6 
```

但是如果我们使用 `reduce` 的话就可以将遍历部分的代码优化为一行代码 

```js
const arr = [1, 2, 3]
const sum = arr.reduce((acc, current) => acc + current, 0)
console.log(sum)
```

对于 `reduce` 来说，它接受两个参数，分别是回调函数和初始值，接下来我们来分解上述代码中 `reduce` 的过程
- 首先初始值为 `0`，该值会在执行第一次回调函数时作为第一个参数传入
- 回调函数接受四个参数，分别为累计值、当前元素、当前索引、原数组，后三者想必大家都可以明白作用，这里着重分析第一个参数
- 在一次执行回调函数时，当前值和初始值相加得出结果 `1`，该结果会在第二次执行回调函数时当做第一个参数传入
- 所以在第二次执行回调函数时，相加的值就分别是 `1` 和 `2`，以此类推，循环结束后得到结果 `6`

想必通过以上的解析大家应该明白 `reduce` 是如何通过回调函数将所有元素最终转换为一个值的，当然 `reduce` 还可以实现很多功能，接下来我们就通过 `reduce` 来实现 `map` 函数

```js
const arr = [1, 2, 3]
const mapArray = arr.map(value => value * 2)
const reduceArray = arr.reduce((acc, current) => {
  acc.push(current * 2)
  return acc
}, [])
console.log(mapArray, reduceArray) // [2, 4, 6]
```

如果你对这个实现还有困惑的话，可以根据上一步的解析步骤来分析过程。

## 2.6 小结
这一章节我们了解了部分 ES6 常考的知识点，其他的一些异步内容我们会放在下一章节去讲。如果大家对于这个章节的内容存在疑问，欢迎在评论区与我互动。

# 3 JS 异步编程及常考面试题

在上一章节中我们了解了常见 ES6 语法的一些知识点。这一章节我们将会学习异步编程这一块的内容，鉴于异步编程是 JS 中至关重要的内容，所以我们将会用三个章节来学习异步编程涉及到的重点和难点，同时这一块内容也是面试常考范围，希望大家认真学习。

## 3.1 并发（concurrency）和并行（parallelism）区别

```!
涉及面试题：并发与并行的区别？
```

异步和这小节的知识点其实并不是一个概念，但是这两个名词确实是很多人都常会混淆的知识点。其实混淆的原因可能只是两个名词在中文上的相似，在英文上来说完全是不同的单词。

并发是宏观概念，我分别有任务 A 和任务 B，在一段时间内通过任务间的切换完成了这两个任务，这种情况就可以称之为并发。

并行是微观概念，假设 CPU 中存在两个核心，那么我就可以同时完成任务 A、B。同时完成多个任务的情况就可以称之为并行。

## 3.2 回调函数（Callback）

```!
涉及面试题：什么是回调函数？回调函数有什么缺点？如何解决回调地狱问题？
```

回调函数应该是大家经常使用到的，以下代码就是一个回调函数的例子：

```js
ajax(url, () => {
    // 处理逻辑
})
```

但是回调函数有一个致命的弱点，就是容易写出回调地狱（Callback hell）。假设多个请求存在依赖性，你可能就会写出如下代码：

```js
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
```

以上代码看起来不利于阅读和维护，当然，你可能会想说解决这个问题还不简单，把函数分开来写不就得了

```js
function firstAjax() {
  ajax(url1, () => {
    // 处理逻辑
    secondAjax()
  })
}
function secondAjax() {
  ajax(url2, () => {
    // 处理逻辑
  })
}
ajax(url, () => {
  // 处理逻辑
  firstAjax()
})
```

以上的代码虽然看上去利于阅读了，但是还是没有解决根本问题。

回调地狱的根本问题就是：

1. 嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身
2. 嵌套函数一多，就很难处理错误

当然，回调函数还存在着别的几个缺点，比如不能使用 `try catch` 捕获错误，不能直接 `return`。在接下来的几小节中，我们将来学习通过别的技术解决这些问题。

## 3.3 Generator

```!
涉及面试题：你理解的 Generator 是什么？
```

`Generator` 算是 ES6 中难理解的概念之一了，`Generator` 最大的特点就是可以控制函数的执行。在这一小节中我们不会去讲什么是 `Generator`，而是把重点放在 `Generator` 的一些容易困惑的地方。

```js
function *foo(x) {
  let y = 2 * (yield (x + 1))
  let z = yield (y / 3)
  return (x + y + z)
}
let it = foo(5)
console.log(it.next())   // => {value: 6, done: false}
console.log(it.next(12)) // => {value: 8, done: false}
console.log(it.next(13)) // => {value: 42, done: true}

```

你也许会疑惑为什么会产生与你预想不同的值，接下来就让我为你逐行代码分析原因

* 首先 `Generator` 函数调用和普通函数不同，它会返回一个迭代器
* 当执行第一次 `next` 时，传参会被忽略，并且函数暂停在 `yield (x + 1)` 处，所以返回 `5 + 1 = 6`
* 当执行第二次 `next` 时，传入的参数等于上一个 `yield` 的返回值，如果你不传参，`yield` 永远返回 `undefined`。此时 `let y = 2 * 12`，所以第二个 `yield` 等于 `2 * 12 / 3 = 8`
* 当执行第三次 `next` 时，传入的参数会传递给 `z`，所以 `z = 13, x = 5, y = 24`，相加等于 `42`

`Generator` 函数一般见到的不多，其实也于他有点绕有关系，并且一般会配合 co 库去使用。当然，我们可以通过 `Generator` 函数解决回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：

```js
function *fetch() {
    yield ajax(url, () => {})
    yield ajax(url1, () => {})
    yield ajax(url2, () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()

```

## 3.4 Promise

```!
涉及面试题：Promise 的特点是什么，分别有什么优缺点？什么是 Promise 链？Promise 构造函数执行和 then 函数执行有什么区别？
```

`Promise` 翻译过来就是承诺的意思，这个承诺会在未来有一个确切的答复，并且该承诺有三种状态，分别是：

1. 等待中（pending）
2. 完成了 （resolved）
3. 拒绝了（rejected）

这个承诺一旦从等待状态变成为其他状态就永远不能更改状态了，也就是说一旦状态变为 resolved 后，就不能再次改变

```
new Promise((resolve, reject) => {
  resolve('success')
  // 无效
  reject('reject')
})

```

当我们在构造 `Promise` 的时候，构造函数内部的代码是立即执行的

```js
new Promise((resolve, reject) => {
  console.log('new Promise')
  resolve('success')
})
console.log('finifsh')
// new Promise -> finifsh
```

`Promise` 实现了链式调用，也就是说每次调用 `then` 之后返回的都是一个 `Promise`，并且是一个全新的 `Promise`，原因也是因为状态不可变。如果你在 `then` 中 使用了 `return`，那么 `return` 的值会被 `Promise.resolve()` 包装

```js
Promise.resolve(1)
  .then(res => {
    console.log(res) // => 1
    return 2 // 包装成 Promise.resolve(2)
  })
  .then(res => {
    console.log(res) // => 2
  })
```

当然了，`Promise` 也很好地解决了回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：

```js
ajax(url)
  .then(res => {
      console.log(res)
      return ajax(url1)
  }).then(res => {
      console.log(res)
      return ajax(url2)
  }).then(res => console.log(res))
```

前面都是在讲述 `Promise` 的一些优点和特点，其实它也是存在一些缺点的，比如无法取消 `Promise`，错误需要通过回调函数捕获。

## 3.5 async 及 await

```!
涉及面试题：async 及 await 的特点，它们的优点和缺点分别是什么？await 原理是什么？
```

一个函数如果加上 `async` ，那么该函数就会返回一个 `Promise`

```js
async function test() {
  return "1"
}
console.log(test()) // -> Promise {<resolved>: "1"}
```

`async` 就是将函数返回值使用 `Promise.resolve()` 包裹了下，和 `then` 中处理返回值一样，并且 `await` 只能配套 `async` 使用

```js
async function test() {
  let value = await sleep()
}
```

`async` 和 `await` 可以说是异步终极解决方案了，相比直接使用 `Promise` 来说，优势在于处理 `then` 的调用链，能够更清晰准确的写出代码，毕竟写一大堆 `then` 也很恶心，并且也能优雅地解决回调地狱问题。当然也存在一些缺点，因为 `await` 将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了 `await` 会导致性能上的降低。

```js
async function test() {
  // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
  // 如果有依赖性的话，其实就是解决回调地狱的例子了
  await fetch(url)
  await fetch(url1)
  await fetch(url2)
}
```

下面来看一个使用 `await` 的例子：

```js
let a = 0
let b = async () => {
  a = a + await 10
  console.log('2', a) // -> '2' 10
}
b()
a++
console.log('1', a) // -> '1' 1
```

对于以上代码你可能会有疑惑，让我来解释下原因

* 首先函数 `b` 先执行，在执行到 `await 10` 之前变量 `a` 还是 0，因为 `await` 内部实现了 `generator` ，`generator` 会保留堆栈中东西，所以这时候 `a = 0` 被保存了下来
* 因为 `await` 是异步操作，后来的表达式不返回 `Promise` 的话，就会包装成 `Promise.reslove(返回值)`，然后会去执行函数外的同步代码
* 同步代码执行完毕后开始执行异步代码，将保存下来的值拿出来使用，这时候 `a = 0 + 10`

上述解释中提到了 `await` 内部实现了 `generator`，其实 `await` 就是 `generator` 加上 `Promise` 的语法糖，且内部实现了自动执行 `generator`。如果你熟悉 co 的话，其实自己就可以实现这样的语法糖。

## 3.6 常用定时器函数

```!
涉及面试题：setTimeout、setInterval、requestAnimationFrame 各有什么特点？
```

异步编程当然少不了定时器了，常见的定时器函数有 `setTimeout`、`setInterval`、`requestAnimationFrame`。我们先来讲讲最常用的`setTimeout`，很多人认为 `setTimeout` 是延时多久，那就应该是多久后执行。

其实这个观点是错误的，因为 JS 是单线程执行的，如果前面的代码影响了性能，就会导致 `setTimeout` 不会按期执行。当然了，我们可以通过代码去修正 `setTimeout`，从而使定时器相对准确

```js
let period = 60 * 1000 * 60 * 2
let startTime = new Date().getTime()
let count = 0
let end = new Date().getTime() + period
let interval = 1000
let currentInterval = interval

function loop() {
  count++
  // 代码执行所消耗的时间
  let offset = new Date().getTime() - (startTime + count * interval);
  let diff = end - new Date().getTime()
  let h = Math.floor(diff / (60 * 1000 * 60))
  let hdiff = diff % (60 * 1000 * 60)
  let m = Math.floor(hdiff / (60 * 1000))
  let mdiff = hdiff % (60 * 1000)
  let s = mdiff / (1000)
  let sCeil = Math.ceil(s)
  let sFloor = Math.floor(s)
  // 得到下一次循环所消耗的时间
  currentInterval = interval - offset 
  console.log('时：'+h, '分：'+m, '毫秒：'+s, '秒向上取整：'+sCeil, '代码执行时间：'+offset, '下次循环间隔'+currentInterval) 

  setTimeout(loop, currentInterval)
}

setTimeout(loop, currentInterval)
```

接下来我们来看 `setInterval`，其实这个函数作用和 `setTimeout` 基本一致，只是该函数是每隔一段时间执行一次回调函数。

通常来说不建议使用 `setInterval`。第一，它和 `setTimeout` 一样，不能保证在预期的时间执行任务。第二，它存在执行累积的问题，请看以下伪代码

```js
function demo() {
  setInterval(function(){
    console.log(2)
  },1000)
  sleep(2000)
}
demo()
```

以上代码在浏览器环境中，如果定时器执行过程中出现了耗时操作，多个回调函数会在耗时操作结束以后同时执行，这样可能就会带来性能上的问题。

如果你有循环定时器的需求，其实完全可以通过 `requestAnimationFrame` 来实现

```js
function setInterval(callback, interval) {
  let timer
  const now = Date.now
  let startTime = now()
  let endTime = startTime
  const loop = () => {
    timer = window.requestAnimationFrame(loop)
    endTime = now()
    if (endTime - startTime >= interval) {
      startTime = endTime = now()
      callback(timer)
    }
  }
  timer = window.requestAnimationFrame(loop)
  return timer
}

let a = 0
setInterval(timer => {
  console.log(1)
  a++
  if (a === 3) cancelAnimationFrame(timer)
}, 1000)
```

首先 `requestAnimationFrame` 自带函数节流功能，基本可以保证在 16.6 毫秒内只执行一次（不掉帧的情况下），并且该函数的延时效果是精确的，没有其他定时器时间不准的问题，当然你也可以通过该函数来实现 `setTimeout`。

## 3.7 小结

异步编程是 JS 中较难掌握的内容，同时也是很重要的知识点。以上提到的每个知识点其实都可以作为一道面试题，希望大家可以好好掌握以上内容如果大家对于这个章节的内容存在疑问，欢迎在评论区与我互动。

```!
异步编程相关内容并非一章节就能讲完，需要继续浏览后续章节。
```

# 4 手写 Promise
在上一章节中我们了解了 `Promise` 的一些易错点，在这一章节中，我们会通过手写一个符合 Promise/A+ 规范的 `Promise` 来深入理解它，并且手写 `Promise` 也是一道大厂常考题，在进入正题之前，推荐各位阅读一下 [Promise/A+ 规范](http://www.ituring.com.cn/article/66566)，这样才能更好地理解这个章节的代码。

## 4.1 实现一个简易版 Promise
在完成符合 Promise/A+ 规范的代码之前，我们可以先来实现一个简易版 `Promise`，因为在面试中，如果你能实现出一个简易版的 `Promise` 基本可以过关了。

那么我们先来搭建构建函数的大体框架

```js
const PENDING = 'pending'
const RESOLVED = 'resolved'
const REJECTED = 'rejected'

function MyPromise(fn) {
  const that = this
  that.state = PENDING
  that.value = null
  that.resolvedCallbacks = []
  that.rejectedCallbacks = []
  // 待完善 resolve 和 reject 函数
  // 待完善执行 fn 函数
}
```

- 首先我们创建了三个常量用于表示状态，对于经常使用的一些值都应该通过常量来管理，便于开发及后期维护
- 在函数体内部首先创建了常量 `that`，因为代码可能会异步执行，用于获取正确的 `this` 对象
- 一开始 `Promise` 的状态应该是 `pending`
- `value` 变量用于保存 `resolve` 或者 `reject` 中传入的值
- `resolvedCallbacks` 和 `rejectedCallbacks` 用于保存 `then` 中的回调，因为当执行完 `Promise` 时状态可能还是等待中，这时候应该把 `then` 中的回调保存起来用于状态改变时使用

接下来我们来完善 `resolve` 和 `reject` 函数，添加在 `MyPromise` 函数体内部

```js
function resolve(value) {
  if (that.state === PENDING) {
    that.state = RESOLVED
    that.value = value
    that.resolvedCallbacks.map(cb => cb(that.value))
  }
}

function reject(value) {
  if (that.state === PENDING) {
    that.state = REJECTED
    that.value = value
    that.rejectedCallbacks.map(cb => cb(that.value))
  }
}
```

这两个函数代码类似，就一起解析了
- 首先两个函数都得判断当前状态是否为等待中，因为规范规定只有等待态才可以改变状态
- 将当前状态更改为对应状态，并且将传入的值赋值给 `value`
- 遍历回调数组并执行

完成以上两个函数以后，我们就该实现如何执行 `Promise` 中传入的函数了

```js
try {
  fn(resolve, reject)
} catch (e) {
  reject(e)
}
```

- 实现很简单，执行传入的参数并且将之前两个函数当做参数传进去
- 要注意的是，可能执行函数过程中会遇到错误，需要捕获错误并且执行 `reject` 函数

最后我们来实现较为复杂的 `then` 函数

```js
MyPromise.prototype.then = function(onFulfilled, onRejected) {
  const that = this
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : r => {
          throw r
        }
  if (that.state === PENDING) {
    that.resolvedCallbacks.push(onFulfilled)
    that.rejectedCallbacks.push(onRejected)
  }
  if (that.state === RESOLVED) {
    onFulfilled(that.value)
  }
  if (that.state === REJECTED) {
    onRejected(that.value)
  }
}
```

- 首先判断两个参数是否为函数类型，因为这两个参数是可选参数
- 当参数不是函数类型时，需要创建一个函数赋值给对应的参数，同时也实现了透传，比如如下代码

    ```js
    // 该代码目前在简单版中会报错
    // 只是作为一个透传的例子
    Promise.resolve(4).then().then((value) => console.log(value))
    ```
    
- 接下来就是一系列判断状态的逻辑，当状态不是等待态时，就去执行相对应的函数。如果状态是等待态的话，就往回调函数中 `push` 函数，比如如下代码就会进入等待态的逻辑
    ```js
    new MyPromise((resolve, reject) => {
      setTimeout(() => {
        resolve(1)
      }, 0)
    }).then(value => {
      console.log(value)
    })
    ```

以上就是简单版 `Promise` 实现，接下来一小节是实现完整版 `Promise` 的解析，相信看完完整版的你，一定会对于 `Promise` 的理解更上一层楼。    

## 4.2 实现一个符合 Promise/A+ 规范的 Promise

这小节代码需要大家配合规范阅读，因为大部分代码都是根据规范去实现的。

我们先来改造一下 `resolve` 和 `reject` 函数

```js
function resolve(value) {
  if (value instanceof MyPromise) {
    return value.then(resolve, reject)
  }
  setTimeout(() => {
    if (that.state === PENDING) {
      that.state = RESOLVED
      that.value = value
      that.resolvedCallbacks.map(cb => cb(that.value))
    }
  }, 0)
}
function reject(value) {
  setTimeout(() => {
    if (that.state === PENDING) {
      that.state = REJECTED
      that.value = value
      that.rejectedCallbacks.map(cb => cb(that.value))
    }
  }, 0)
}
```

- 对于 `resolve` 函数来说，首先需要判断传入的值是否为 `Promise` 类型
- 为了保证函数执行顺序，需要将两个函数体代码使用 `setTimeout` 包裹起来

接下来继续改造 `then` 函数中的代码，首先我们需要新增一个变量 `promise2`，因为每个 `then` 函数都需要返回一个新的 `Promise` 对象，该变量用于保存新的返回对象，然后我们先来改造判断等待态的逻辑

```js
if (that.state === PENDING) {
  return (promise2 = new MyPromise((resolve, reject) => {
    that.resolvedCallbacks.push(() => {
      try {
        const x = onFulfilled(that.value)
        resolutionProcedure(promise2, x, resolve, reject)
      } catch (r) {
        reject(r)
      }
    })

    that.rejectedCallbacks.push(() => {
      try {
        const x = onRejected(that.value)
        resolutionProcedure(promise2, x, resolve, reject)
      } catch (r) {
        reject(r)
      }
    })
  }))
}
```

- 首先我们返回了一个新的 `Promise` 对象，并在 `Promise` 中传入了一个函数
- 函数的基本逻辑还是和之前一样，往回调数组中 `push` 函数
- 同样，在执行函数的过程中可能会遇到错误，所以使用了 `try...catch` 包裹
- 规范规定，执行 `onFulfilled` 或者 `onRejected` 函数时会返回一个 `x`，并且执行 `Promise` 解决过程，这是为了不同的 `Promise` 都可以兼容使用，比如 JQuery 的 `Promise` 能兼容 ES6 的 `Promise`

接下来我们改造判断执行态的逻辑

```js
if (that.state === RESOLVED) {
  return (promise2 = new MyPromise((resolve, reject) => {
    setTimeout(() => {
      try {
        const x = onFulfilled(that.value)
        resolutionProcedure(promise2, x, resolve, reject)
      } catch (reason) {
        reject(reason)
      }
    })
  }))
}
```

- 其实大家可以发现这段代码和判断等待态的逻辑基本一致，无非是传入的函数的函数体需要异步执行，这也是规范规定的
- 对于判断拒绝态的逻辑这里就不一一赘述了，留给大家自己完成这个作业

最后，当然也是最难的一部分，也就是实现兼容多种 `Promise` 的 `resolutionProcedure` 函数

```js
function resolutionProcedure(promise2, x, resolve, reject) {
  if (promise2 === x) {
    return reject(new TypeError('Error'))
  }
}
```

首先规范规定了 `x` 不能与 `promise2` 相等，这样会发生循环引用的问题，比如如下代码

```js
let p = new MyPromise((resolve, reject) => {
  resolve(1)
})
let p1 = p.then(value => {
  return p1
})
```

然后需要判断 `x` 的类型

```js
if (x instanceof MyPromise) {
    x.then(function(value) {
        resolutionProcedure(promise2, value, resolve, reject)
    }, reject)
}
```

这里的代码是完全按照规范实现的。如果 `x` 为 `Promise` 的话，需要判断以下几个情况：

1. 如果 `x` 处于等待态，`Promise` 需保持为等待态直至 `x` 被执行或拒绝
2. 如果 `x` 处于其他状态，则用相同的值处理 `Promise`

当然以上这些是规范需要我们判断的情况，实际上我们不判断状态也是可行的。

接下来我们继续按照规范来实现剩余的代码

```js
let called = false
if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
  try {
    let then = x.then
    if (typeof then === 'function') {
      then.call(
        x,
        y => {
          if (called) return
          called = true
          resolutionProcedure(promise2, y, resolve, reject)
        },
        e => {
          if (called) return
          called = true
          reject(e)
        }
      )
    } else {
      resolve(x)
    }
  } catch (e) {
    if (called) return
    called = true
    reject(e)
  }
} else {
  resolve(x)
}
```
- 首先创建一个变量 `called` 用于判断是否已经调用过函数
- 然后判断 `x` 是否为对象或者函数，如果都不是的话，将 `x` 传入 `resolve` 中
- 如果 `x` 是对象或者函数的话，先把 `x.then` 赋值给 `then`，然后判断 `then` 的类型，如果不是函数类型的话，就将 `x` 传入 `resolve` 中
- 如果 `then` 是函数类型的话，就将 `x` 作为函数的作用域 `this` 调用之，并且传递两个回调函数作为参数，第一个参数叫做 `resolvePromise` ，第二个参数叫做 `rejectPromise`，两个回调函数都需要判断是否已经执行过函数，然后进行相应的逻辑
- 以上代码在执行的过程中如果抛错了，将错误传入 `reject` 函数中

以上就是符合 Promise/A+ 规范的实现了，如果你对于这部分代码尚有疑问，欢迎在评论中与我互动。

## 4.3 小结

这一章节我们分别实现了简单版和符合 Promise/A+ 规范的 `Promise`，前者已经足够应付大部分面试的手写题目，毕竟写出一个符合规范的 `Promise` 在面试中不大现实。后者能让你更加深入地理解 `Promise` 的运行原理，做技术的深挖者。