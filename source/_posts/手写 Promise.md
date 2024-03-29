---
title: 手写 Promise
date: 2020-07-27 00:00:00
updated: 2020-07-27 00:00:00
tags:
  - Promise
---

现在前端面试越来越高深莫测了，居然还要手写 Promise。不会啊，怎么办！赶紧自己借鉴一下，手撸一个。

<!-- more -->

只实现基础功能和

- Promise 肯定是一个类，同时还定义了 resolve 和 reject 方法 ;
- Promise 有三个状态 `pending`、`fulfilled`、`rejected`;
- Promise 的 prototype 上有 then 方法，then 方法会返回一个新的 Promise，可实现链式调用。

```
function Promise(executor) {
  let self = this

  // 初始的状态
  self.status = 'pending'
  // 成功后的值
  self.value = undefined
  // 失败的原因
  self.reason = undefined

  // 成功后的回调
  self.onResolved = []
  // 失败后的回调
  self.onRejected = []

  function resolve(value) {
    if (self.status === 'pending') {
      self.status = 'fullfilled'
      self.value = value
      self.onResolved.map((fn) => fn())
    }
  }
  function reject(reason) {
    if (self.status === 'pending') {
      self.status = 'rejected'
      self.reason = reason
      self.onRejected.map((fn) => fn())
    }
  }

  try {
    // 会传2个参数（函数）
    executor(resolve, reject)
  } catch (error) {
    reject(error)
  }
}

Promise.prototype.then = function (onFullfilled, onRejected) {
  let self = this
  let promise2

  onFullfilled =
    typeof onFullfilled === 'function' ? onFullfilled : function () {}
  onRejected = typeof onRejected === 'function' ? onRejected : function () {}

  if (self.status === 'fullfilled') {
    return (promise2 = new Promise(function (resolve, reject) {
      // 异步调用
      setTimeout(() => {
        try {
          var x = onFullfilled(self.value)
          resolvePromise(promise2, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }))
  }
  if (self.status === 'rejected') {
    return (promise2 = new Promise(function (resolve, reject) {
      setTimeout(() => {
        try {
          var x = onRejected(self.reason)
          resolvePromise(promise2, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }))
  }
  if (self.status === 'pending') {
    return (promise2 = new Promise(function (resolve, reject) {
      self.onResolved.push(function () {
        try {
          var x = onFullfilled(self.value)
          resolvePromise(promise2, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
      self.onRejected.push(function () {
        try {
          var x = onRejected(self.reason)
          resolvePromise(promise2, x, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }))
  }
  // 返回值不可预估， resolvePromise负责把各种稀奇古怪的 x 包装成一个正常的Promise
  function resolvePromise(promise2, x, resolve, reject) {
    if (promise2 === x) {
      return reject(new TypeError('Chaining cycle detected for promise!'))
    }
    if (x instanceof Promise) {
      x.then(
        function (data) {
          resolve(data)
        },
        function (e) {
          reject(e)
        }
      )
      return
    }

    if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
      try {
        var then = x.then
        var called
        if (typeof then === 'function') {
          then.call(
            x,
            (y) => {
              if (called) {
                return
              }
              called = true
              return resolvePromise(promise2, y, resolve, reject)
            },
            (r) => {
              if (called) {
                return
              }
              called = true
              return reject(r)
            }
          )
        } else {
          resolve(x)
        }
      } catch (e) {
        if (called) {
          return
        }
        return reject(e)
      }
    } else {
      resolve(x)
    }
  }
}
```

> 引用

- [可能是目前最易理解的手写 promise](https://juejin.im/post/5dc383bdf265da4d2d1f6b23#heading-3)
