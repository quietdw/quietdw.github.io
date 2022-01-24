---
title: Promise
date: 2019-03-14 00:00:00
updated: 2019-03-14 00:00:00
tags:
  - Promise
---

之前在写 ajax 方面的博客的时候，简单的介绍了一下 Promise 在 ajax 里的应用，这次详细的梳理一下 Promise 功能以及原理。

<!-- more -->

# 含义

首先，Promise 是一个对象，也是一个构造函数。Promise 的设计思想是所有异步任务都返回一个 Promise 实例。Promise 实例有一个 then 方法，用来指定下一步的回调函数。这样可以让异步操作写起来就像在写同步操作的流程，而不必一层层地嵌套回调函数。

```javascrpt

// 传统写法
step1(function (value1) {
  step2(value1, function(value2) {
    step3(value2, function(value3) {
      step4(value3, function(value4) {
        // ...
      });
    });
  });
});


// Promise 的写法
var doSomething = new Promise()
doSomething.then('第一件事').then('第二件事')
```

上面的例子如果是用回调函数实现会非常麻烦。

Promise 对象的状态不受外界影响。对象有三个状态，`pending`、`fulfilled`、`rejected`。一旦状态发生改变（pending -> fulfilled,fulfilled-> reject），就不会在变。

Promise 也有缺点，首先，无法取消 Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise 内部抛出的错误，不会反应到外部。第三，当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

# 基本用法

下面代码创造了一个 Promise 实例。

```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value)
  } else {
    reject(error)
  }
})
```

实例生成后，可以用 then 方法指定 resolve 和 reject 的回调函数，then 的第一个参数是成功的回调函数，第二个是失败的回调函数。

Promise 新建后就会立即执行。

```javascript
let promise = new Promise(function (resolve, reject) {
  console.log('Promise')
  resolve()
})

promise.then(function () {
  console.log('resolved.')
})

console.log('Hi!')

// Promise
// Hi!
// resolved
```

下面是异步加载图片的例子。

```javascript
function loadImageAsync(url) {
  return new Promise(function (resolve, reject) {
    const image = new Image()

    image.onload = function () {
      resolve(image)
    }

    image.onerror = function () {
      reject(new Error('Could not load image at ' + url))
    }

    image.src = url
  })
}
```

上面代码中，使用 Promise 包装了一个图片加载的异步操作。如果加载成功，就调用 resolve 方法，否则就调用 reject 方法。

上面代码中，Promise 新建后立即执行，所以首先输出的是 Promise。然后，then 方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以 resolved 最后输出。

调用 resolve 或 reject 并不会终结 Promise 的参数函数的执行

```javascript
new Promise((resolve, reject) => {
  resolve(1)
  console.log(2)
}).then(r => {
  console.log(r)
})
// 2
// 1
```

上面代码中，调用 resolve(1)以后，后面的 console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务.

# Promise.prototype.then()

Promise 实例具有 then 方法，也就是说，then 方法是定义在原型对象。then 方法返回的是一个新的 Promise 实例（注意，不是原来那个 Promise 实例）。因而可以采用链式写法，即 then 方法后面再调用另一个 then 方法。

# Promise.prototype.catch()

Promise.prototype.catch 方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个 catch 语句捕获。

一般来说，不要在 then 方法里面定义 Reject 状态的回调函数（即 then 的第二个参数），总是使用 catch 方法。

如果 Promise 状态已经变成 resolved，再抛出错误是无效的。因为 Promise 的状态一旦发生改变，就不会再变。

# Promise.prototype.finally()

finally 方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

finally 本质上是 then 方法的特例。

finally 的实现：

```javascript
promise.finally(() => {
  // 语句
})

// 等同于
promise.then(
  result => {
    // 语句
    return result
  },
  error => {
    // 语句
    throw error
  }
)

Promise.prototype.finally = function (callback) {
  let P = this.constructor
  return this.then(
    value => P.resolve(callback()).then(() => value),
    reason =>
      P.resolve(callback()).then(() => {
        throw reason
      })
  )
}
```

# Promise.all()

Promise.all 方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```javascript
const p = Promise.all([p1, p2, p3])
```

上面代码中，Promise.all 方法接受一个数组作为参数，p1、p2、p3 都是 Promise 实例，如果不是，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理。（Promise.all 方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。）

p 的状态由 p1、p2、p3 决定，分成两种情况。

（1）只有 p1、p2、p3 的状态都变成 fulfilled，p 的状态才会变成 fulfilled，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。

（2）只要 p1、p2、p3 之中有一个被 rejected，p 的状态就变成 rejected，此时第一个被 reject 的实例的返回值，会传递给 p 的回调函数。

注意，如果作为参数的 Promise 实例，自己定义了 catch 方法，那么它一旦被 rejected，并不会触发 Promise.all()的 catch 方法。 因为改函数执行完自己的 catch(也会变成 resolve。

# Promise.race()

Promise.race 方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。和 all()不同的是，只要 p1、p2、p3 之中有一个实例率先改变状态，p 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 p 的回调函数。

# Promise.resolve()

Promise.resolve 方法可以将现有对象转换成 Promise 对象。

Promise.resolve 方法的参数分成四种情况。

1. 参数是一个 Promise 实例：Promise.resolve 将不做任何修改、原封不动地返回这个实例。
2. 参数是一个带 then 方法的对象：Promise.resolve 方法会将这个对象转为 Promise 对象，然后就立即执行该对象的 then 方法。
3. 参数不是具有 then 方法的对象，或根本就不是对象：如果参数是一个原始值，或者是一个不具有 then 方法的对象，则 Promise.resolve 方法返回一个新的 Promise 对象，状态为 resolved。
4. 不带有任何参数：Promise.resolve 方法允许调用时不带参数，直接返回一个 resolved 状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用 Promise.resolve 方法。

# Promise.reject()

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为 rejected。

注意，Promise.reject()方法的参数，会原封不动地作为 reject 的理由，变成后续方法的参数。这一点与 Promise.resolve 方法不一致。

```javascript
const thenable = {
  then(resolve, reject) {
    reject('出错了')
  }
}

Promise.reject(thenable).catch(e => {
  console.log(e === thenable)
})

// true
```

> 引用

- [Promise 对象](https://wangdoc.com/javascript/async/promise.html)
- [阮一峰 Promise 对象](http://es6.ruanyifeng.com/#docs/promise)
