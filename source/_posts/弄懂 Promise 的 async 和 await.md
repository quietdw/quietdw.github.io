---
title: 弄懂 Promise 的 async 和 await
date: 2019-04-24 00:00:00
updated: 2019-04-24 00:00:00
tags:
  - Promise
  - async
  - await
---

在 MDN 中，关于 async function 是这么解释的：async function 声明用于定义一个返回 `AsyncFunction` 对象的异步函数。异步函数是指通过事件循环异步执行的函数，它会通过一个隐式的 `Promise` 返回其结果。但是如果你的代码使用了异步函数，它的语法和结构会更像是标准的同步函数.

<!-- more -->

初读的时候一知半解，但大概了解 async 和 Promise 有关，是一个异步函数，但是像同步函数。

通过例子更直观的了解一下，日常是这么使用 Promise 的：

```javascript
function fn() {
  return new Promise((resolve, reject) => {
    // 两个钩子
    setTimeout(() => {
      let a = 5
      resolve(a)
    }, 1000)
  })
}

fn().then(x => {
  // 注：这个函数和 resolve 不是同一个函数
  console.log(x)
})
```

可以 通过 async await 改写：

```javascript
function fn() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let a = 5
      resolve(a)
    }, 1000)
  })
}

async function test() {
  // 内部函数有异步操作，所以要用 async 标识
  let n = await fn()
  console.log(n)
}

test()
```

await 一定要和 async 一起使用，单独使用会报错。

除了 resolve ，还要处理 reject ：

```javascript
function fn() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let a = 5
      if (a < 3) {
        resolve(a)
      } else {
        reject(a) // 会执行这一步
      }
    }, 1000)
  })
}

async function test() {
  try {
    let n = await fn()
    console.log(n)
  } catch (error) {
    console.log(`error:${error}`) // error:5
  }
}

test() // error:5
```

上面两个 await 的例子，可能没有 then 简洁，但是逻辑很“同步”，当有需要处理问题时，不会太容易出错。

await 后面只能跟一个 Promise，当需要处理多个 PRomise 时，请使用 Promise.all()

> 引用

- [async function
  ](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)
