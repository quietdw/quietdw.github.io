---
title: JS里的数据类型转换
tags:
  - JavaScript
  - 内存
---

JS 里有许多方式可以实现数据类型的转换。以下主要讲的是`number`、`string`、`boolean`这三种类型的转换。

<!-- more -->

任意类型 x

# x 转换成 number

主要是 string 转换成 number

- 通过`Number(x)`

```javascript
Number('123') // 123
Number('') // 0
Number('0x11') // 17
Number('0b11') // 3
Number('0o11') // 9
Number('foo') // NaN
Number('100a') // NaN
```

- 通过`parseInt(x,10)` [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseInt)
- 通过`parseFloat(x)` [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/parseFloat)
- 通过数值运算符`+`或者`x-0`

# x 转换成 boolean

- 通过 Boolean(x)
- 通过`!!x`

5 个 falsy 值：0,NaN,null,'',undefined，这五个值转换成布尔值都为 false，其余都是 true

# x 转换成 string

主要是 number 转换成 string，object 转换后的值是`[object Object]`

- x.toString()
- String(x)
- x + ''

# 关于对象和普通类型数据的区别

object 是复杂数据类型，普通类型的数据是简单数据类型，两者在内存中的存放位置是不一样的。

JS 引擎将内存分为代码区和数据区。数据区可分为堆内存和栈内存。普通类型的数据是存储在 stack（栈内存）中的，而 object 的数据是存储在 heap（堆内存）中，其地址是存在栈内存中。

我们通过以下例子，更深入理解两者区别：

```javascript
var a = 1
var b = a //b=1
b = 2 //不会影响 a
//请问 a 显示是几？  2
```

```javascript
var a = { name: 'a' } //在栈内存有一个地址，引用堆内存中的数据{name: 'a'}
var b = a // 在栈内存有一个与a一样的地址，引用堆内存中的数据{name: 'a'}
b = { name: 'b' } //在栈内存有一个与b一样的地址，引用堆内存中的数据{name: 'b'}
//请问现在 a.name 是多少？  'a'
```

```javascript
var a = { name: 'a' }
var b = a
b.name = 'b' //修改了其引用地址的数据的name属性
//请问现在 a.name 是多少？ 'b'
```

```javascript
var a = { name: 'a' }
var b = a
b = null //只是在栈内存中，b的地址的值变为null，堆内存中的数据没用改动
//请问现在 a 是什么？ 'a'
```

上面的第一个例子，改变 b 不会影响 a，我们把它称为深拷贝，基本类型的赋值操作都是深拷贝。

上面第二个例子，改变 b 影响 a，我们把它称为浅拷贝。对象操作也有深拷贝,就是对 Heap 内存也进行完全的拷贝。

上面第四个例子，如果加上`a=null`，则堆内存中的数据没有被引用，其内存空间将会被回收，至于什么时候回收？不知道。在 IE 里有个内存泄漏的 Bug，当绑定事件在结束后没有加`xxx=null`,在页面关闭时，其所占内存不会被释放，除非关闭整个浏览器。
