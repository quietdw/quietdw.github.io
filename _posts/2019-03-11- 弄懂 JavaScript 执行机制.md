---
title: 弄懂 JavaScript 执行机制
tags:
  - macroTask
  - microTask
  - 宏任务
  - 微任务
  - JavaScript
---

以前以为 JavaScript 是按照语句执行顺序执行的，直到现在被打脸了

<!-- more -->

举个的例子：

```JavaScript
console.log('script start');

setTimeout(function() {
  console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
  console.log('promise1');
}).then(function() {
  console.log('promise2');
});

console.log('script end');
```

按照以前的思路，运行结果应该是

```javascript
script start
setTimeout
promise1
promise2
script end
```

去 Chrome 浏览器里验证了一下

```javascript
script start
script end
promise1
promise2
setTimeout
```

而且令人抓狂的是，不同浏览器输出的结果也不尽相同，下文的结果均以 Chrome 的为例。

要了解为什么会输出这样的结果，我们需要了解 JavaScript 运行机制。

首先我们要了解 event loop 是怎么处理 macrotasks 和 microtasks 。

JavaScript 是单线程的，这个 event loop 是持续的运行，而且有许多 macrotasks 在队列等待被执行，但是是浏览器选择怎么执行的。一次鼠标点击事件的回调需要一次 macrotasks ，同样包括解析 HTML， 也包括 setTimeout。

setTimeout 等待当前 macrotasks 结束，才能执行。这也解释 为什么 setTimeout 在 script end 后面，因为输出 script end 也是第一个 macrotasks 部分之一。

**macrotasks 执行完后不会马上执行下一个 macrotasks，而是会处理当前 macrotasks 产生的 microtasks 队列，然后才会执行下一个 macrotasks。microtasks 一般是异步的操作，比如回调什么的。在处理 microtasks 时产生的 microtasks 也会被加到当前 macrotasks 队列中，最终也会被执行。**

除了广义的同步任务和异步任务，我们对任务有更精细的定义：

**macro-task(宏任务)：包括整体代码 script，setTimeout，setInterval**

**micro-task(微任务)：Promise，process.nextTick**

这样我们就能解释上面代码为什么会出现那样的结果了

首先解析 JavaScript 是第一个 macrotasks。

从上往下执行，先打印 `script start`

遇到 setTimeout，不执行，扔到下一个 macrotasks；

遇到 promise ，是异步的，不执行，扔到当前 macrotasks 的 microtasks 中；

到最后一行，打印 `script end`；

然后再去 microtasks 看看有没有事情要做；

然后 打印 `promise1` 和 `promise1`，promise 返回 undefined；

然后再到下一个 macrotasks，发现是 setTimeout，打印 `setTimeout`。

关于 setTimeout 要补充的是：即便主线程为空，0 毫秒实际上也是达不到的。根据 HTML 的标准，最低是 4 毫秒。有兴趣的同学可以自行了解。

觉得自己弄懂了吗？

做个简单的小例子

```javascript
console.log("1");

setTimeout(function() {
  console.log("2");
  Promise.resolve().then(function() {
    console.log("3");
  });
  new Promise(function(resolve) {
    console.log("4");
    resolve();
  }).then(function() {
    console.log("5");
  });
});
Promise.resolve().then(function() {
  console.log("6");
});
new Promise(function(resolve) {
  console.log("7");
  resolve();
}).then(function() {
  console.log("8");
});

setTimeout(function() {
  console.log("9");
  Promise.resolve().then(function() {
    console.log("10");
  });
  new Promise(function(resolve) {
    console.log("11");
    resolve();
  }).then(function() {
    console.log("12");
  });
});
```

在Chrome输出结果是：1,7,6,8,2,4,3,5,9,11,10,12

> 引用

- [这一次，彻底弄懂 JavaScript 执行机制](https://juejin.im/post/59e85eebf265da430d571f89#heading-4)
- [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
