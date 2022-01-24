---
title: mixin、科里化，高阶函数
date: 2019-03-09 00:00:00
updated: 2019-03-09 00:00:00
tags:
  - mixin
  - 柯里化
  - 高阶函数
  - JavaScript
  - bind
---

了解一下一些 mixin、柯里化、高阶函数。

<!-- more -->

# mixin

用代码解释一下：

```javascript
function (a, b) {
    for (key in b) {
        a[key] = b[key]
    }
}
```

上面代码能完成把对象 b 中的属性全部覆盖到 a 中。

ES6 中 Object.assign 也是同样的功能

# 柯里化 currying

说到柯里化，就有必要说一下 bind() 方法。

bind 方法将函数绑定到一个对象，它还附带一些其他应用：除了第一个实参之外，传入 bind() 的实参也会绑定到 this。

这个附带应用有时也被称为“柯里化”

举个例子：

```javascript
var sum = function (x, y) {
  return x + y
}

// 创建一个类似sum的新函数，但 this 的值是 null
// 并且第一个参数绑定到1，这个新函数期望只传入一个实参
var succ = sum.bind(null, 1)
succ(2) // 3 x绑定到1，并传入2作为实参

function f(y, z) {
  return this.x + y + z
}
var g = f.bind({ x: 1 }, 2) // 绑定this和y
g(3) // 6 this.x绑定到1，y绑定2，z绑定3
```

简单的来说，柯里化是将函数中一个或多个变量设为定值，并得到一个新函数。

```javascript
var cache = []
// 当add没传参数时，返回之前传入参数之和
function add(n) {
  if (n === undefined) {
    return cache.reduce((i, j) => i + j, 0)
  } else {
    cache.push(n)
    return add
  }
}

add(1)(2)(3)() // 6

// 进阶版
function add() {
  let args = [].slice.call(arguments)
  let fn = function () {
    let fn_args = [].slice.call(arguments)
    return add.apply(null, args.concat(fn_args))
  }
  fn.toString = function () {
    return args.reduce((a, b) => a + b)
  }
  return fn
}
```

# 高阶函数

所谓高阶函数 higher-order function ，就是操作函数的函数。它接收一个或多个函数作为参数，并返回一个新函数。

比如 bind() 也可以实现高阶函数:

```javascript
function add1(x, y) {
  return x + y
}

// 主动调用bind
add2 = Function.prototype.bind.call(add1, undefined, 1) //将add1传入，this的值绑定到undefined，x的值绑定到1

add2(2) //3
```
