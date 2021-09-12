> 参考 [光光同学---一次弄懂Event Loop（彻底解决此类面试问题）](https://juejin.im/post/5c3d8956e51d4511dc72c200?utm_source=gold_browser_extension#comment)

## 1 Event Loop 整体

在JavaScript中，任务被分为两种：
- 一种**宏**任务（MacroTask）（也叫`Task`）：**script全部代码**、**setTimeout**、**setInterval**、setImmediate（浏览器暂时不支持，只有IE10支持，具体可见MDN）、I/O、UI Rendering。
- 一种叫**微**任务（MicroTask）：**Promise**、Process.nextTick（Node独有）、Object.observe(废弃)、MutationObserver（具体使用方式查看这里）

## 2 浏览器中的 Event Loop

Javascript 有一个 main thread （主线程）和 call-stack （调用栈)，所有的任务都会被放到*调用栈*等待*主线程*执行。

### 2.1 同步任务和异步任务

Javascript 单线程任务被分为同步任务和异步任务：
- 同步任务会在*调用栈*中按照顺序等待主线程依次执行
- 异步任务会在异步任务**有了结果后**（比如 Ajax 取回数据? setTimeOut 到时间再放入?），将注册的回调函数放入*任务队列*中等待*主线程*空闲的时候（调用栈被清空），被读取到*调用栈*内等待*主线程*的执行。

### 2.2 事件模型概述

撇去所有细节：
1. 执行完所有的 script 全部代码
2. 此时去检查微任务队列（看有没有Promise.then），如果不为空，则全部执行完。
3. 继续检查宏任务队列（setTimeOut、setInterval），如果不为空：执行一个，再回到微任务队列执行全部；再执行一个，再回到微任务队列执行全部；如此往复...

#### 2.2.1 举个例子

```js
console.log('script start');

setTimeout(function() {
  console.log('setTimeout start');
  new Promise((resolve, reject) => {        // 自己加戏
      console.log('inner Promise');
      resolve();
  }).then(() => {
      console.log('inner promise11');
  }).then(() => {
      console.log('inner promise22');
  });
  console.log('setTimeout end');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});
console.log('script end');

/*
    answer
*/

// script start
// script end           // 1. 执行完所有的 script 全部代码
// promise1
// promise2             // 2. 此时去检查微任务队列，如果不为空，则全部执行完
// setTimeout start     // 3. 继续检查宏任务队列，如果有，执行一个；此时又往微任务队列里扔任务了 Promise.then
// inner Promise
// setTimeout end
// inner promise11      // 4. 检查了一下微任务，有任务全执行
// inner promise22
                        // 5. 检查一下宏任务，没任务
```

多看几遍这个动图：

![EventLoop执行过程](https://user-gold-cdn.xitu.io/2019/1/18/16860ae5ad02f993?imageslim)

#### 2.2.2 再举个例子

```js
console.log('script start')

async function async1() {
  await async2()
  console.log('async1 end')
}
async function async2() {
  console.log('async2 end') 
}
async1()

setTimeout(function() {
  console.log('setTimeout')
}, 0)

new Promise(resolve => {
  console.log('Promise')
  resolve()
})
  .then(function() {
    console.log('promise1')
  })
  .then(function() {
    console.log('promise2')
  })

console.log('script end')

/*
    answer
*/
// script start
// async2 end       // 相当于 new promise；后面的代码相当于 .then 内容，放入微任务
// Promise
// script end
                    //---同步代码执行完毕，去看看微任务
// asnync1 end
// promise1
// promise2
                    //---所有微任务执行完毕，去看看宏任务
// setTimeout
```

1. 先理解 `async/await` 语法糖

```js
async function async1() {
  await async2()
  console.log('async1 end')     // await 余下代码
}
async function async2() {
  console.log('async2 end') 
}

async1()

// 相当于如下代码（自己理解）：
new Promise((resolve, reject) => {
    async2();
    resolve();
}).then(() => {
    console.log('async1 end');      // await 余下代码 包装入 .then 中
});

```

2. 关于73以下版本和73版本的区别

- 在老版本版本以下，先执行 promise1 和 promise2，再执行 async1 。
- 在73版本，**先执行** async1 再执行 promise1 和 promise2 。

## 3 NodeJS的 Event Loop