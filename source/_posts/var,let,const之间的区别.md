---
title: var,let,const之间的区别
tags:
  - var
  - let
  - const
---

首先声明变量都放在作用域第一行！然后再写一下 var,let,const之间的区别。

<!-- more -->
# var

var在mdn中是被这描述的：

变量声明，无论发生在何处，即使代码未执行，都在执行任何代码之前进行处理。

将赋值给未声明变量的值在执行赋值时将其**隐式地创建为全局变量**（它将成为全局对象的属性）。但有时也不一定如此，有时多层嵌套函数时，如果上一层声明了a，内部使用a就不会生成全局变量。



声明变量在任何代码执行前创建，而非声明变量只有在执行赋值操作的时候才会被创建

未能声明变量将很可能导致意想不到的结果。因此，建议始终声明变量，无论它们是在函数还是全局作用域内。 在 ECMAScript 5 严格模式下，分配给未声明的变量会引发错误。

由于变量提升，变量可以在其声明之前使用。

```jacascript
a = 5
var a

//可以理解为

var a
a = 5
```

因此，建议始终在作用域顶部声明变量（全局代码的顶部和函数代码的顶部），这可以清楚知道哪些变量是函数作用域（本地），哪些变量在作用域链上的。

# let

let允许你声明一个作用域被限制在块级中的变量、语句或者表达式。与var关键字不同的是，var声明的变量只能是全局或者整个函数块的。

let声明的变量只在其声明的块或子块中可用，这一点，与var相似。二者之间最主要的区别在于var声明的变量的作用域是整个封闭函数。

在 let 声明变量前使用该变量会报错（Temp Dead Zooe）

```javascript
function varTest() {
  var x = 1;
  if (true) {
    var x = 2;  // 同样的变量!
    console.log(x);  // 2
  }
  console.log(x);  // 2
}

function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // 不同的变量
    console.log(x);  // 2
  }
  console.log(x);  // 1
}
```

在(同一个函数或)同一个作用域中用let重复定义一个变量将引起 TypeError。

# const

const是块级作用域，很像使用 let 语句定义的变量。const的值不能通过重新赋值来改变，并且不能重新声明。

const创建一个常数，这个常数可以是全局的或者是局部的。 全局的常量不能成为`window`的属性，不像`var`。

const声明创建一个值的只读引用。但这并不意味着它所持有的值是不可变的，只是变量标识符不能重新分配。例如，在引用内容是对象的情况下，这意味着可以改变对象的内容（例如，其参数）。

常量在声明的时候可以使用大小写，但通常情况下全部用大写字母。


> 引用
- [var mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/var)
- [let mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let)
- [const mdn](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/const)