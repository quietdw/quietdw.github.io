---
title: JS里的数据类型
date: 2018-11-25 00:00:00
updated: 2018-11-25 00:00:00
tags:
  - JavaScript
  - 数据类型
---

JavaScript 的数据类型，共有六种。（ES6 又新增了第七种 Symbol 类型的值)，所以现在有七种，分别为：

<!-- more -->

- number
- string
- boolean
- symbol
- undefined
- null
- object 复杂类型，由以上 6 种简单类型数据组成

# number

JavaScript 内部，所有数字都是以 64 位浮点数形式存储的，即是整数也是。所以 1 与 1.0 是相同的（1===1.0 返回`true`），是同一个数。

由于浮点数不是精确的值，所以涉及小数的比较和运算要小心

## 范围及精度

JavaScript 能够表示的数值范围为 2<sup>1024</sup>到 2<sup>-1023</sup>（开区间），超出这个范围的数无法表示，返回 Infinity 或者 0;

Javascript 提供 Number 对象 MAX_VALUE 和 MIN_VALUE 属性，返回表示的最大值和最小值

JavaScript 对-2<sup>53</sup>到 2<sup>53</sup>的数值，都可以精确表示，简单表示的话对 15 位的十进制数都可以精确处理

## 进制

JavaScript 对整数提供四种进制的表示方法：十进制、十六进制、八进制、二进制

- 十进制：没有前导 0 的数值。
- 八进制：有前缀 0o 或 0O 的数值，或者有前导 0、且只用到 0-7 的八个阿拉伯数字的数值。
- 十六进制：有前缀 0x 或 0X 的数值。
- 二进制：有前缀 0b 或 0B 的数值。

默认情况下，JavaScript 内部会自动将八进制、十六进制、二进制转为十进制

如果八进制、十六进制、二进制的数值里面，出现不属于该进制的数字，就会报错

通常来说，有前导 0 的数值会被视为八进制，但是如果前导 0 后面有数字 8 和 9，则该数值被视为十进制

## 特殊数值

NaN 是 JavaScript 的特殊值，表示“非数字”（Not a Number），主要出现在将字符串解析成数字出错的场合。NaN 不等于任何值，包括它本身。

NaN 与任何数（包括它自己）的运算，得到的都是 NaN。

## 与数值相关的全局方法

### parseInt()

parseInt 方法用于将字符串转为整数（返回值十进制，或者 NaN）。

字符串转为整数的时候，是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分。

如果 parseInt 的第一个参数**不是字符串**，会被先转为字符串**String(010)的结果是 9**，对于八进制的前缀 0，尤其需要注意。

如果**字符串**以 0x 或 0X 开头，parseInt 会将其按照十六进制数解析。如果字符串以 0 开头，将其按照 10 进制解析。

parseInt 方法还可以接受第二个参数（2 到 36 之间），表示被解析的值的进制，返回该值对应的十进制数。默认情况下，parseInt 的第二个参数为 10，即默认是十进制转十进制。超出这个范围，则返回 NaN。

如果第二个参数是 0、undefined 和 null，则直接忽略。

如果字符串包含对于指定进制无意义的字符，则从最高位开始，只返回可以转换的数值。

如果最高位无法转换，则直接返回 NaN。

### parseFloat()

parseFloat 方法用于将一个字符串转为浮点数。

如果字符串符合科学计数法，则会进行相应的转换。

如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回 NaN。

### isNaN()

isNaN 方法可以用来判断一个值是否为 NaN。

判断 NaN 更可靠的方法是，利用 NaN 为唯一不等于自身的值的这个特点，进行判断。

### isFinite()

isFinite 方法返回一个布尔值，表示某个值是否为正常的数值。

除了 Infinity、-Infinity、NaN 和 undefined 这几个值会返回 false，isFinite 对于其他的数值都会返回 true。

# string

字符串就是零个或多个排在一起的字符，放在单引号或双引号之中。如果要在单引号字符串的内部，使用单引号，就必须在内部的单引号前面加上反斜杠，用来转义。双引号字符串内部使用双引号，也是如此。此外，因为字符串是不允许多行的，如果想多行显示，建议使用`+`连接。

## 转义

反斜杠（\）在字符串内有特殊含义，用来表示一些特殊字符，所以又称为转义符。

反斜杠还有三种特殊用法。

- 反斜杠后面紧跟三个八进制数（000 到 377），代表一个字符。HHH 对应该字符的 Unicode 码点，比如\251 表示版权符号。显然，这种方法只能输出 256 种字符。
- \x 后面紧跟两个十六进制数（00 到 FF），代表一个字符。HH 对应该字符的 Unicode 码点，比如\xA9 表示版权符号。这种方法也只能输出 256 种字符。
- \u 后面紧跟四个十六进制数（0000 到 FFFF），代表一个字符。XXXX 对应该字符的 Unicode 码点，比如\u00A9 表示版权符号。

## 字符串与数组

字符串可以被视为字符数组，因此可以使用数组的方括号运算符，用来返回某个位置的字符（位置编号从 0 开始）。

如果方括号中的数字超过字符串的长度，或者方括号中根本不是数字，则返回 undefined。

但是，字符串与数组的相似性仅此而已。实际上，无法改变字符串之中的单个字符。

length 属性返回字符串的长度，该属性也是无法改变的

## 字符集

JavaScript 使用 Unicode 字符集。JavaScript 引擎内部，所有字符都用 Unicode 表示。

JavaScript 不仅以 Unicode 储存字符，还允许直接在程序中使用 Unicode 码点表示字符，即将字符写成\uxxxx 的形式，其中 xxxx 代表该字符的 Unicode 码点。比如，\u00A9 代表版权符号。

我们还需要知道，每个字符在 JavaScript 内部都是以 16 位（即 2 个字节）的 UTF-16 格式储存。也就是说，JavaScript 的单位字符长度固定为 16 位长度，即 2 个字节。对于码点在 U+10000 到 U+10FFFF 之间的字符，长度为 32 位（即 4 个字节），而且前两个字节在 0xD800 到 0xDBFF 之间，后两个字节在 0xDC00 到 0xDFFF 之间。举例来说，码点 U+1D306 对应的字符为 𝌆，它写成 UTF-16 就是 0xD834 0xDF06。
对于码点在 U+10000 到 U+10FFFF 之间的字符，JavaScript 总是认为它们是两个字符（length 属性为 2）。所以处理的时候，必须把这一点考虑在内，也就是说，JavaScript 返回的字符串长度可能是不正确的。

## Base64 转码

Base64 是一种基于 64 个可打印字符来表示二进制数据的表示方法。

全局函数 btoa() 能将字符串转为 Base64 编码。
全局函数 atob() 能将 Base64 编码转为原来的编码。

要将非 ASCII 码字符转为 Base64 编码，必须中间插入一个转码环节

```javascript
function b64Encode(str) {
  return btoa(encodeURIComponent(str))
}

function b64Decode(str) {
  return decodeURIComponent(atob(str))
}

b64Encode('你好') // "JUU0JUJEJUEwJUU1JUE1JUJE"
b64Decode('JUU0JUJEJUEwJUU1JUE1JUJE') // "你好"
```

# boolean

注意，空数组（[]）和空对象（{}）对应的布尔值，都是 true。

# Symbol

ES6 引入了一种新的原始数据类型 Symbol，表示独一无二的值。

Symbol 值通过 Symbol 函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的 Symbol 类型。凡是属性名属于 Symbol 类型，就都是独一无二的，可以保证不会与其他属性名产生冲突。

注意，Symbol 函数前不能使用 new 命令，否则会报错。

Symbol 值不能与其他类型的值进行运算，会报错。

## Symbol.for()，Symbol.keyFor()

有时，我们希望重新使用同一个 Symbol 值，Symbol.for 方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 Symbol 值。如果有，就返回这个 Symbol 值，否则就新建并返回一个以该字符串为名称的 Symbol 值。

```javascript
let s1 = Symbol.for('foo')
let s2 = Symbol.for('foo')

s1 === s2 // true
```

Symbol.keyFor 方法返回一个已登记的 Symbol 类型值的 key。

```javascript
let s1 = Symbol.for('foo')
Symbol.keyFor(s1) // "foo"

let s2 = Symbol('foo')
Symbol.keyFor(s2) // undefined
```

# undefined,null

一般两者没区别，只是按照惯例**undefined**表示一个变量未赋值，**null**表示一个 object 未赋值。

# object

简单说，对象就是一组“键值对”（key-value）的集合，是一种无序的复合数据集合。

```javascript
var o1 = {}
var o2 = new Object()
var o3 = Object.create(Object.prototype)
```

以上三种方法都能建一个对象。

对象的所有键名都是字符串（ES6 又引入了 Symbol 值也可以作为键名），所以加不加引号都可以。

如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符），且也不是数字，则必须加上引号，否则会报错。

对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为“方法”，它可以像函数那样调用。

```javascript
var obj = {
  p: function (x) {
    return 2 * x
  }
}

obj.p(1) // 2
```

如果属性的值还是一个对象，就形成了链式引用。

```javascript
var o1 = {}
var o2 = { bar: 'hello' }

o1.foo = o2
o1.foo.bar // "hello"
```

## 对象的引用

如果不同的变量名指向同一个对象，那么它们都是这个对象的引用，也就是说指向同一个内存地址。修改其中一个变量，会影响到其他所有变量。

```javascript
var o1 = {}
var o2 = o1

o1.a = 1
o2.a // 1

o2.b = 2
o1.b // 2
```

但是，这种引用只局限于对象，如果两个变量指向同一个原始类型的值。那么，变量这时都是值的拷贝。

## 表达式还是语句？

对象采用大括号表示，这导致了一个问题：如果行首是一个大括号，它到底是表达式还是语句？

JavaScript 引擎读到上面这行代码，会发现可能有两种含义。第一种可能是，这是一个表达式，表示一个包含 foo 属性的对象；第二种可能是，这是一个语句，表示一个代码区块，里面有一个标签 foo，指向表达式 123。

为了避免这种歧义，V8 引擎规定，如果行首是大括号，一律解释为对象。不过，为了避免歧义，最好在大括号前加上圆括号。

```javascript
eval('{foo: 123}') // 123
eval('({foo: 123})') // {foo: 123}
```

上面代码中，如果没有圆括号，eval 将其理解为一个代码块；加上圆括号以后，就理解成一个对象。

## 属性的操作

读取对象的属性，有两种方法，一种是使用点运算符，还有一种是使用方括号运算符。

```javascript
var foo = 'bar';
var obj = {
  p: 'Hello World',
  bar: 'whosyourdaddy'
};

obj.p // "Hello World"
obj['p'] // "Hello World"
obj.[foo] // "whosyourdaddy"
```

请注意，如果使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理。

查看一个对象本身的所有属性，可以使用 Object.keys 方法。

delete 命令用于删除对象的属性(键名和键值一并删除)，删除成功后返回 true。

注意，删除一个不存在的属性，delete 不报错，而且返回 true。

另外，需要注意的是，delete 命令只能删除对象本身的属性，无法删除继承的属性。

```javascript
var obj = {}
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

## in 运算符

in 运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回 true，否则返回 false。它的左边是一个字符串，表示属性名，右边是一个对象。

in 运算符的一个问题是，它不能识别哪些属性是对象自身的，哪些属性是继承的。就像上面代码中，对象 obj 本身并没有 toString 属性，但是 in 运算符会返回 true，因为这个属性是继承的。

这时，可以使用对象的 hasOwnProperty 方法判断一下，是否为对象自身的属性。

```javascript
var obj = {}
if ('toString' in obj) {
  console.log(obj.hasOwnProperty('toString')) // false
}
```

## 属性的遍历：for...in 循环

for...in 循环用来遍历一个对象的全部属性。

for...in 循环有两个使用注意点。

- 它遍历的是对象所有可遍历（enumerable）的属性，会跳过不可遍历的属性。
- 它不仅遍历对象自身的属性，还遍历继承的属性。

如果继承的属性是可遍历的，那么就会被 for...in 循环遍历到。但是，一般情况下，都是只想遍历对象自身的属性，所以使用 for...in 的时候，应该结合使用 hasOwnProperty 方法，在循环内部判断一下，某个属性是否为对象自身的属性。

## with 语句

with 语句的格式如下：

```javascript
with (对象) {
  语句
}
```

它的作用是操作同一个对象的多个属性时，提供一些书写的方便。

注意，如果 with 区块内部有变量的赋值操作，必须是当前对象已经存在的属性，否则会创造一个当前作用域的全局变量。

单纯从上面的代码块，根本无法判断 x 到底是全局变量，还是对象 obj 的一个属性。这非常不利于代码的除错和模块化，编译器也无法对这段代码进行优化，只能留到运行时判断，这就拖慢了运行速度。因此，建议不要使用 with 语句，可以考虑用一个临时变量代替 with。

```javascript
with (obj1.obj2.obj3) {
  console.log(p1 + p2)
}

// 可以写成
var temp = obj1.obj2.obj3
console.log(temp.p1 + temp.p2)
```

> 引用

- https://github.com/ruanyf/es6tutorial/blob/3c44084f4b2e318fcbec77b7191b1f2412726c47/docs/symbol.md
- https://wangdoc.com/javascript/types/index.html
