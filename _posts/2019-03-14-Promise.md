---
title: Promise
tags:
  - Promise
---

之前在写 ajax 方面的博客的时候，简单的介绍了一下 Promise 在 ajax 里的应用，这次详细的梳理一下Promise 功能以及原理。

<!-- more -->

# 含义

首先，Promise 是一个对象，也是一个构造函数。Promise 的设计思想是所有异步任务都返回一个 Promise 实例。Promise 实例有一个then方法，用来指定下一步的回调函数。这样可以让异步操作写起来就像在写同步操作的流程，而不必一层层地嵌套回调函数。

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

Promise 也有缺点，首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。第三，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

# 基本用法

下面代码创造了一个Promise实例。

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
let promise = new Promise(function(resolve, reject) {
  console.log('Promise');
  resolve();
});

promise.then(function() {
  console.log('resolved.');
});

console.log('Hi!');

// Promise
// Hi!
// resolved
```

下面是异步加载图片的例子。

```javascript
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();

    image.onload = function() {
      resolve(image);
    };

    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```

上面代码中，使用Promise包装了一个图片加载的异步操作。如果加载成功，就调用resolve方法，否则就调用reject方法。

上面代码中，Promise 新建后立即执行，所以首先输出的是Promise。然后，then方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行，所以resolved最后输出。

调用resolve或reject并不会终结 Promise 的参数函数的执行

```javascript
new Promise((resolve, reject) => {
  resolve(1);
  console.log(2);
}).then(r => {
  console.log(r);
});
// 2
// 1
```

上面代码中，调用resolve(1)以后，后面的console.log(2)还是会执行，并且会首先打印出来。这是因为立即 resolved 的 Promise 是在本轮事件循环的末尾执行，总是晚于本轮循环的同步任务.

# Promise.prototype.then()

Promise 实例具有then方法，也就是说，then方法是定义在原型对象。then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因而可以采用链式写法，即then方法后面再调用另一个then方法。

# Promise.prototype.catch()

Promise.prototype.catch方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于指定发生错误时的回调函数。

Promise 对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个catch语句捕获。

一般来说，不要在then方法里面定义 Reject 状态的回调函数（即then的第二个参数），总是使用catch方法。

如果 Promise 状态已经变成resolved，再抛出错误是无效的。因为Promise的状态一旦发生改变，就不会再变。

# Promise.prototype.finally()

finally方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。

finally本质上是then方法的特例。

finally的实现：

```javascript

promise
.finally(() => {
  // 语句
});

// 等同于
promise
.then(
  result => {
    // 语句
    return result;
  },
  error => {
    // 语句
    throw error;
  }
);

Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
    value  => P.resolve(callback()).then(() => value),
    reason => P.resolve(callback()).then(() => { throw reason })
  );
};
```

# Promise.all()

Promise.all方法用于将多个 Promise 实例，包装成一个新的 Promise 实例。

```javascript
const p = Promise.all([p1, p2, p3]);
```

上面代码中，Promise.all方法接受一个数组作为参数，p1、p2、p3都是 Promise 实例，如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为 Promise 实例，再进一步处理。（Promise.all方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。）

p的状态由p1、p2、p3决定，分成两种情况。

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。 因为改函数执行完自己的catch(也会变成resolve。

# Promise.race()

Promise.race方法同样是将多个 Promise 实例，包装成一个新的 Promise 实例。和all()不同的是，只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

# Promise.resolve()

Promise.resolve方法可以将现有对象转换成Promise对象。

Promise.resolve方法的参数分成四种情况。

1. 参数是一个Promise实例：Promise.resolve将不做任何修改、原封不动地返回这个实例。
2. 参数是一个带then方法的对象：Promise.resolve方法会将这个对象转为 Promise 对象，然后就立即执行该对象的then方法。
3. 参数不是具有then方法的对象，或根本就不是对象：如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态为resolved。
4. 不带有任何参数：Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法。

# Promise.reject()

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。

注意，Promise.reject()方法的参数，会原封不动地作为reject的理由，变成后续方法的参数。这一点与Promise.resolve方法不一致。

```javascript
const thenable = {
  then(resolve, reject) {
    reject('出错了');
  }
};

Promise.reject(thenable)
.catch(e => {
  console.log(e === thenable)
})

// true
```

> 引用
- [Promise 对象](https://wangdoc.com/javascript/async/promise.html)
- [阮一峰 Promise 对象](http://es6.ruanyifeng.com/#docs/promise)