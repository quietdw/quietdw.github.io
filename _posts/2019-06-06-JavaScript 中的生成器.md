---
title: avaScript 中的生成器
tags:
  - ES6
  - JavaScript
  - 生成器
---


在看公司之前项目的时候，发现代码中某些函数前有`*`符号，然后函数内部也有`yield`，心想这是什么写法，难道 JavaScript 又更新了吗，翻了一下资料，发现这写法早就有了，在ES6中叫生成器。

<!-- more -->

Generator 函数有多种理解角度。
    
从语法上，可以理解 Generator 函数是一个状态机，封装了多个内部状态。
    
从形式上，Generator 函数是一个普通函数，但有个特征，一是function关键字与函数名之间有一个星号`function* foo(x, y) { ··· }`；二是函数内部使用`yield`表达式，定义不同的内部状态。

Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该**函数并不执行，返回的也不是函数运行结果**，而**是一个指向内部状态的指针对象**。 下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

yield表达式后面的表达式，只有当调用next方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

yield表达式与return语句既有相似之处，也有区别。相似之处在于，都能返回紧跟在语句后面的那个表达式的值。区别在于每次遇到yield，函数暂停执行，下一次再从该位置继续向后执行，而return语句不具备位置记忆的功能。一个函数里面，只能执行一次（或者说一个）return语句，但是可以执行多次（或者说多个）yield表达式。正常函数只能返回一个值，因为只能执行一次return；Generator 函数可以返回一系列的值，因为可以有任意多个yield。从另一个角度看，也可以说 Generator 生成了一系列的值。

任意一个对象的Symbol.iterator方法，等于该对象的遍历器生成函数，调用该函数会返回该对象的一个遍历器对象。

```javascript
var myIterable = {};
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};

for (let value of myIterable) { 
    console.log(value); 
}
// 1
// 2
// 3

//or

console.log([...myIterable]); // [1, 2, 3]
```

yield表达式本身没有返回值，或者说总是返回undefined。next方法可以带一个参数，该参数就会被当作上一个yield表达式的返回值。
    
```javascript
function* foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);
  return (x + y + z);
  }

var a = foo(5);
a.next() // Object{value:6, done:false}
a.next() // Object{value:NaN, done:false}
a.next() // Object{value:NaN, done:true}
var b = foo(5);
b.next() // { value:6, done:false }
b.next(12) // { value:8, done:false }  2*12/3
b.next(13) // { value:42, done:true } 5 + 24 +13
```

for...of循环可以自动遍历 Generator 函数运行时生成的Iterator对象，且此时不再需要调用next方法。