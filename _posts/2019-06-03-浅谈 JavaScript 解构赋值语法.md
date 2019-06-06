---
title: 浅谈 JavaScript 解构赋值语法
tags:
  - ES6
  - JavaScript
  - 解构赋值
---


最近工作项目中，使用了解构赋值语法，感觉非常爽，但是有些写法只是有印象，每次都要查一下，非常麻烦，故写这篇博客巩固一下知识。

<!-- more -->

什么是解构赋值？MDN上是这么描述的:

> 解构赋值语法是一种 Javascript 表达式，它使得将值从数组，或属性从对象，提取到不同的变量中，成为可能。 

# 解构数组

## 变量声明并赋值时的解构

```javascript
var foo = [1,2,3];

var [a,b,c] = foo;
console.log(a); // 1
console.log(c); // 3
```

## 变量先声明后赋值时的解构

```javascript
var a,b
[a,b]=[1,2]
console.log(a); // 1
console.log(b); // 2
```

## 默认值

```javascript
var a,b
[a=1,b=2]=[3]
console.log(a); // 3
console.log(b); // 2
```

## 交换变量

在ES6之前交换变量都需要一个临时变量。

```javascript
var a=1
var b=2
[a,b]=[b,a]
console.log(a); // 2
console.log(b); // 1
```

## 忽略某些返回值
```javascript
function f() {
  return [1, 2, 3];
}

var [a, , b] = f();
console.log(a); // 1
console.log(b); // 3
```

## 将剩余数组赋值给一个变量

注意剩余元素必须是最后一个元素

```javascript
var [a, ...b] = [1, 2, 3];
console.log(a); // 1
console.log(b); // [2, 3]
```

# 解构对象

## 基本赋值

和数组类似

```javascript
var o = {p: 42, q: true};
var {p, q} = o;

console.log(p); // 42
console.log(q); // true
```

## 无声明赋值

和数组的类似，经常在项目中获取数据时用到。`{a, b} = {a: 1, b: 2}` 不是有效的独立语法，因为左边的` {a, b}` 被认为是一个块而不是对象字面量。`({a, b} = {a: 1, b: 2}) `是有效的，正如 `var {a, b} = {a: 1, b: 2}`

## 给新的变量名赋值

```javascript
var o = {p: 42, q: true};
var {p: foo, q: bar} = o;
 
console.log(foo); // 42 
console.log(bar); // true
```

## 默认值

```javascript
var {a = 10, b = 5} = {a: 3}; 

console.log(a); // 3
console.log(b); // 5
```

## 给新的变量命名并提供默认值

```javascript
var {a:aa = 10, b:bb = 5} = {a: 3};

console.log(aa); // 3
console.log(bb); // 5
```

## 函数参数默认值

```javascript
function drawES2015Chart({size = 'big', cords = { x: 0, y: 0 }, radius = 25} = {}) 
{
  console.log(size, cords, radius);
  // do some chart drawing
}

drawES2015Chart({
  cords: { x: 18, y: 30 },
  radius: 30
});
```

## 从作为函数实参的对象中提取数据

```javascript
function userId({id}) {
  return id;
}

function whois({displayName: displayName, fullName: {firstName: name}}){
  console.log(displayName + " is " + name);
}

var user = { 
  id: 42, 
  displayName: "jdoe",
  fullName: { 
      firstName: "John",
      lastName: "Doe"
  }
};

console.log("userId: " + userId(user)); // "userId: 42"
whois(user); // "jdoe is John"
```

## 对象属性计算名和解构

```javascript
let key = "z";
let { [key]: foo } = { z: "bar" };

console.log(foo); // "bar"
```

> 引用
- [解构赋值语](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)