---
title: ES6学习笔记
date: 2017-11-08 09:43:41
tags:
- javaScript
- ES6
categories:
- 学习笔记
description: 《ECMAScript6入门——阮一峰》学习笔记
---

# let、const

## let

### 基本用法

ES6新增let命令，用来声明变量。用法类似var，但是所声明变量，只在let命令所在的代码块内有效。

```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

for循环的计数器，就很适合用let命令。

```
for (let i = 0; i < 10; i++){
  // ...
}
console.log(i);
// ReferenceError: i is not defined
```

### 不存在变量提升

var命令会发生“变量提升”现象。let命令没有，let声明的变量一定要在声明后使用，否则会报错：

```
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;
```

### 暂时性死区

ES6规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

只要块级作用域内存在`let`命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。

```
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

### 不允许重复声明

let命令不允许在相同作用域内，重复声明同一个变量。

## 块级作用域

let实际上为JS新增了块级作用域。

ES6允许块级作用域的任意嵌套。

外层作用域无法读取内层作用域的变量。

内层作用域可以定义外层作用域的同名变量。

块级作用域的出现，使得立即执行函数表达式不再必要。

+ ES5中，函数只能声明在顶层作用域或函数作用域中。
+ ES6中，明确允许在块级作用域中声明函数。

## do表达式

本质上，块级作用域是一个语句，没有返回值。

现在有一个提案，使得块级作用域可以变为表达式，也就是说有返回值。办法就是在块级作用域前面加上do，变成do表达式，**返回内部最后执行的表达式的值。**

```
let x = do {
  let t = f();
  t * t + 1;
}
```

## const

const声明一个只读常量，一旦声明，常量的值就不能改变。

const一旦声明了变量，就立刻初始化，不能留到以后赋值；如果只声明不赋值，会报错。

const命令和let命令一样，只在声明所在的块级作用域内有效。

const声明的常量也不提示，同样存在暂时性死区，只能在声明的位置后面使用。

const命令和和let命令一样，不能重复声明。

### const本质：

`const`实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指针，`const`只能保证这个指针是固定的，至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

```
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only

```

上面代码中，常量`foo`储存的是一个地址，这个地址指向一个对象。不可变的只是这个地址，即不能把`foo`指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。

## ES6声明变量的6中方法

除了ES5的两种var和function，还有let、const、import、class。

## 顶层对象的属性

ES6规定，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。



# 变量的解构赋值

## 数组的解构赋值

### 基本用法

```
let [a,b,c] = [1,2,3];

// 等同于
let a = 1;
let b = 2;
let c = 3;
```

```
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

```
let [x, y] = [1, 2, 3];
x // 1
y // 2

let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```

如果等号右边不是数组，会报错。

```
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

### 默认值：

+ 解构赋值允许指定默认值。

  ```
  let [foo = true] = [];
  foo // true

  let [x, y = 'b'] = ['a']; // x='a', y='b'
  let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
  ```

  ​	注意，ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，如果一个数组成员不严格等于`undefined`，默认值是不会生效的。

  ```
  let [x = 1] = [undefined];
  x // 1

  let [x = 1] = [null];
  x // null
  ```

+ 默认值可以是一个表达式，但这个表达式是惰性求值的，只有在用到的时候，才会被求值。

  ```
  function f(){
    console.log('aaa');
  }

  let [x = f()] = [1];
  ```

  ​上面代码中，因为x能取到值，所以函数f根本不会执行。

+ 默认值可以引用解构赋值的其他变量，但是该变量必须已经声明。

  ```
  let [x = 1, y = x] = [];     // x=1; y=1
  let [x = 1, y = x] = [2];    // x=2; y=2
  let [x = 1, y = x] = [1, 2]; // x=1; y=2
  let [x = y, y = 1] = [];     // ReferenceError
  ```

  ​最后一个表达式之所以会报错，是因为x用到默认值y时，y还没有声明。

## 对象的解构赋值

### 基本用法

对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。

```
let { bar, foo } = { foo: "aaa", bar: "bbb" };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: "aaa", bar: "bbb" };
baz // undefined
```

如果变量名与属性名不一致，必须写成下面这样。

```
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```

这实际上说明，对象的解构赋值是下面形式的简写。

```
let { foo: foo, bar: bar} = { foo: "aaa", bar: "bbb" };
```

也就是说，对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。

```
let { foo: baz } = { foo: "aaa", bar: "bbb" };
baz // "aaa"
foo // error: foo is not defined
```

上面代码中，`foo`是匹配的模式，`baz`才是变量。真正被赋值的是变量`baz`，而不是模式`foo`。

嵌套：

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```

```
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```

```
const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

let { loc, loc: { start }, loc: { start: { line }} } = node;
line // 1
loc  // Object {start: Object}
start // Object {line: 1, column: 5}
```

嵌套赋值的例子：

```
let obj = {};
let arr = [];

({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });

obj // {prop:123}
arr // [true]
```

### 默认值

```
var {x = 3} = {};
x // 3

var {x, y = 5} = {x: 1};
x // 1
y // 5

var {x: y = 3} = {};
y // 3

var {x: y = 3} = {x: 5};
y // 5

var { message: msg = 'Something went wrong' } = {};
msg // "Something went wrong"
```

+ 默认值生效的条件是，对象的属性值严格等于undefined。

  ```
  var { x = 3 } = { x: undefined };
  x // 3

  var { x = 3 } = { x: null };
  x // null
  ```

  如果解构失败，变量的值等于undefined。

  ```
  let {foo} = {bar: 'baz'};
  foo // undefined
  ```

+ 如果解构模式是嵌套的对象，而且自对象所在的赋属性不存在，那么将会报错。

+ 如果一个已经声明的变量用于解构赋值，必须非常小心

  ```
  // 错误的写法
  let x;
  {x} = {x: 1};
  // SyntaxError: syntax error
  ```

  上面代码会报错，因为JavaScript引擎会将{x}理解成一个代码块，从而发生于法错误。只有不将大括号写在行首，避免JavaScript将其解释为代码块，才能解决问题。

  ```
  // 正确的写法
  let x;
  ({x} = {x: 1});
  ```

+ 对象的解构赋值，可以很方便地将现有对象的方法，复制到某个变量。

  ```
  let { log, sin, cos } = Math;
  ```

  上面代码将Math对象的对数、正弦、余弦三个方法，赋值到对应的变量上。

## 函数参数的解构赋值

函数参数也可以使用解构赋值

```
function add ([x, y]){
  return x + y;
}

add([1,2]); // 3
```

上面代码中，函数add的参数表面上是一个数组，但在传入参数的那一刻，数组参数就被解构成变量x和y。

函数参数的解构也可以使用默认值。

```
function move ({x = 0, y = 0} = []){
  return [x ,y];
}

move({x: 3, y: 8}); // [3,8]
move({x: 3}); // [3,0]
move({}); // [0,0]
move(); // [0,0]
```



# 函数的扩展

## 函数参数的默认值

ES6之前，不能直接为函数的参数指定默认值。

ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。

```
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
```

+ 参数变量是默认声明的，所以在函数体内不能用let或const再次声明，否则会报错。

  ```
  function(x = 2){
    let x = 1; // error
    const x = 3; // error
  }
  ```

+ 使用参数默认值时，函数不能有同名参数。

  ```
  // 不报错
  function (x,x,y){
    //...
  }

  // 报错
  function (x,x,y = 1){
    //...
  }
  ```

+ 参数默认值不是传值，而是每次调用函数，都重新计算默认值表达式的值。

  ```
  let x = 99;
  function( p = x + 1){
    onsole.log(p);
  }

  foo() // 100

  x = 100;
  foo() // 101
  ```

+ 参数默认值可以和解构赋值的默认值，结合起来使用。

  ```
  function foo ({x, y = 5}){
    console.log(x, y);
  }

  foo({}) // undefined 5
  foo({x: 1}) // 1 5
  foo({x: 1, y: 2}) // 1 2
  foo() // TypeError: Cannot read property 'x' of undefined
  ```

  上面代码只是用了对象的解构赋值默认值，没有使用函数参数的默认值。只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。

  ```
  function foo({x, y = 5} = {}) {
    console.log(x, y);
  }

  foo() // undefined 5
  ```

+ 参数默认值的位置：

  定义了默认值的参数，应该是函数的尾参数。如果非尾部的参数设置默认值，这个参数没发省略，除非显示输入undefined。

  ```
  function f(x = 1, y){
    return [x, y];
  }

  f() // [1, undefined]
  f(2) // [2, undefined]
  f(, 1) // 报错
  f(undefined, 1) // [1,1]
  ```

+ 参数作用域：

  一旦设置的参数的默认值，函数进行声明初始化时，参数会单独形成一个作用域，等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值的时候，是不会出现的。

  ```
  var x = 1;
  function f(x, y = x){
    console.log(y);
  }

  f(2) // 2
  ```

  ```
  let x = 1;
  function f(x = y){
    let x = 2;
    console.log(y);
  }

  f() // 1
  ```

## rest参数

ES6引入rest参数（形式为…变量名），用于获取函数的多余参数，这样就不需要使用arguments对象了。rest参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

下面是一个rest参数代替arguments变量的例子：

```
// arguments变量的写法
function sortNumbers(){
  return Array.prototype.slice.call(arguments).sort();
}

// rest写法
const sortNumbers = (...numbers) => numbers.sort();
```

arguments对象不是数组，而是类似数组的对象，所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转换为数组。rest参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用。

+ rest参数之后不能有其他参数，否则报错。

  ```
  // 报错
  function f(a, ...b, c){
    // ...
  }
  ```

## 箭头函数

ES6允许使用“箭头”（=>）定义函数。

```
var f = v => v;

// 等同于

var f = function (v){
  return v;
}
```

+ 如果箭头函数不需要参数或者需要多个参数，就使用一个圆括号代表参数部分。

  ```
  var f = () => 5;
  // 等同于
  var f = function (){
    return 5;
  }

  var sum = (num1, num2) => num1 + num2;
  // 等同于
  var sum = function(num1, num2){
    return num1+ num2;
  }
  ```

+ 如果箭头函数的代码块部分多余一条语句，就要用大括号括起来，并且使用return返回

  ```
  var sum = (num1, num2) => { return num1 + num2; }
  ```

+ 由于大括号被解释为代码块，所以箭头函数如果返回一个对象，必须在对象外面加上括号，否则会报错。

  ```
  // 报错
  let getTempItem = id => { id: id, name: "Temp" };

  // 不报错
  let getTempItem = id => ({ id: id, name: "Temp" });
  ```

+ 箭头函数的一个作用是简化回调函数

  ```
  // 正常函数写法
  [1,2,3].map(function (x) {
    return x * x;
  });

  // 箭头函数写法
  [1,2,3].map(x => x * x);
  ```

### 使用注意

1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。

```
function foo() {
  setTimeout(() => {
    console.log('id:', this.id);
  }, 100);
}

var id = 21;

foo.call({ id: 42 });
// id: 42
```

上面代码中，`setTimeout`的参数是一个箭头函数，这个箭头函数的定义生效是在`foo`函数生成时，而它的真正执行要等到 100 毫秒后。如果是普通函数，执行时`this`应该指向全局对象`window`，这时应该输出`21`。但是，箭头函数导致`this`总是指向函数定义生效时所在的对象（本例是`{id: 42}`），所以输出的是`42`。

`this`指向的固定化，并不是因为箭头函数内部有绑定`this`的机制，实际原因是箭头函数根本没有自己的`this`，导致内部的`this`就是外层代码块的`this`。正是因为它没有`this`，所以也就不能用作构造函数。

```
function foo() {
  return () => {
    return () => {
      return () => {
        console.log('id:', this.id);
      };
    };
  };
}

var f = foo.call({id: 1});

var t1 = f.call({id: 2})()(); // id: 1
var t2 = f().call({id: 3})(); // id: 1
var t3 = f()().call({id: 4}); // id: 1
```

上面代码之中，只有一个`this`，就是函数`foo`的`this`，所以`t1`、`t2`、`t3`都输出同样的结果。因为所有的内层函数都是箭头函数，都没有自己的`this`，它们的`this`其实都是最外层`foo`函数的`this`。

除了`this`，以下三个变量在箭头函数之中也是不存在的，指向外层函数的对应变量：`arguments`、`super`、`new.target`。

另外，由于箭头函数没有自己的`this`，所以当然也就不能用`call()`、`apply()`、`bind()`这些方法去改变`this`的指向。

```
(function() {
  return [
    (() => this.x).bind({ x: 'inner' })()
  ];
}).call({ x: 'outer' });
// ['outer']
```

2. 不可以当作构造函数，也就是说，不可以使用`new`命令，否则会抛出一个错误。
3. 不可以使用`arguments`对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
4. 不可以使用`yield`命令，因此箭头函数不能用作 Generator 函数。

# Promise对象

## 基本用法

ES6规定，Promise对象是一个构造函数，用来生成Promise实例

```
const promise = new Promise(function(resolve, reject){
  // ...some code
  
  if(/*异步操作成功*/){
    resolve(value);
  }else{
    reject(error);
  }
})
```

# Class的基本用法

## 简介

JS语言中，生成实例对象的传统方法是构造函数。

```
function Point(x, y){
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function (){
  return '(' + this.x + ', ' + this.y + ')';
};

var p = new Point(1, 2);
```

ES6提供了更接近传统语言的写法，引入了**Class（类）**这个概念，作为对象的模版。通过Class关键字，可以定义类。

基本上，ES6 的`class`可以看作只是一个语法糖，它的绝大部分功能，ES5 都可以做到，新的`class`写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已。上面的代码用 ES6 的`class`改写，就是下面这样。

```
// 定义类
class Point {
  constructor(x, y){
    this.x = x;
    this.y = y;
  }
  toString(){
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```

+ 上面定义类一个“类”，里面有一个constructor方法，这就是构造方法，而`this`关键字则代表实例对象，也就是说，ES5点构造函数Point，对应ES6的Point类的构造方法。

+ Point类除了构造方法，还定义了一个toString方法。定义“类”方法的时候，前面不需要家`function`关键字。

+ 方法之间不需要逗号间隔，加了会报错。

+ ES6的类，完全可以看成是构造函数的另一种写法：

  ```
  class Point {
    // ...
  }

  typeof Point // "function"
  Point === Point.prototype.constructor // true
  ```

  上面代码表明，类的数据类型就是函数，类本身就指向构造函数。

+ 使用的时候，也是直接对类使用new命令，跟构造函数的用法一致。

  ```
  class Bar {
    doStuff(){
      console.log('stuff');
    }
  }

  var b = new Bar();
  b.doStuff(); // "stuff"
  ```

+ 构造函数的`prototype`属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面。

  ```
  class Point {
    constructor() {
      // ...
    }

    toString() {
      // ...
    }

    toValue() {
      // ...
    }
  }

  // 等同于

  Point.prototype = {
    constructor() {},
    toString() {},
    toValue() {},
  };
  ```

  在类的实例上面调用方法，其实就是调用原型上的方法。

  ```
  class B {}
  let b = new B();

  b.constructor === B.prototype.constructor // true

  ```

  上面代码中，`b`是`B`类的实例，它的`constructor`方法就是`B`类原型的`constructor`方法。

+ 由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。

  ```
  class Point {
    constructor(){
      // ...
    }
  }

  Object.assign(Point.prototype, {
    toString(){},
    toValue(){}
  });
  ```

+ `prototype`对象的`constructor`属性，直接指向“类”的本身，这与 ES5 的行为是一致的。

  ```
  Point.prototype.constructor === Point // true
  ```

+ 类的内部所有定义的方法，都是不可枚举的（non-enumerable）。

  ```
  class Point {
    constructor(x, y) {
      // ...
    }

    toString() {
      // ...
    }
  }

  Object.keys(Point.prototype)
  // []
  Object.getOwnPropertyNames(Point.prototype)
  // ["constructor","toString"]

  ```

  上面代码中，`toString`方法是`Point`类内部定义的方法，它是不可枚举的。这一点与 ES5 的行为不一致。

+ 类的属性名，可以采用表达式。

  ```
  let methodName = 'getArea';

  class Square {
    constructor(length) {
      // ...
    }

    [methodName]() {
      // ...
    }
  }
  ```

## 严格模式

类和模块的内部，默认就是严格模式，所以不需要使用`use strict`指定运行模式。

## constructor方法

+ `constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。

+ 一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

  ```
  class Point {
  }

  // 等同于
  class Point {
    constructor() {}
  }
  ```

+ `constructor`方法默认返回实例对象（即`this`），完全可以指定返回另外一个对象。

  ```
  class Foo {
    constructor() {
      return Object.create(null);
    }
  }

  new Foo() instanceof Foo
  // false

  ```

  上面代码中，`constructor`函数返回一个全新的对象，结果导致实例对象不是`Foo`类的实例。

+ 类必须使用`new`调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用`new`也可以执行。

## 类的实例

+ 与 ES5 一样，实例的属性除非显式定义在其本身（即定义在`this`对象上），否则都是定义在原型上（即定义在`class`上）。

  ```
  //定义类
  class Point {

    constructor(x, y) {
      this.x = x;
      this.y = y;
    }

    toString() {
      return '(' + this.x + ', ' + this.y + ')';
    }

  }

  var point = new Point(2, 3);

  point.toString() // (2, 3)

  point.hasOwnProperty('x') // true
  point.hasOwnProperty('y') // true
  point.hasOwnProperty('toString') // false
  point.__proto__.hasOwnProperty('toString') // true

  ```

  上面代码中，`x`和`y`都是实例对象`point`自身的属性（因为定义在`this`变量上），所以`hasOwnProperty`方法返回`true`，而`toString`是原型对象的属性（因为定义在`Point`类上），所以`hasOwnProperty`方法返回`false`。这些都与 ES5 的行为保持一致。

+ 与 ES5 一样，类的所有实例共享一个原型对象。

  ```
  var p1 = new Point(2,3);
  var p2 = new Point(3,2);

  p1.__proto__ === p2.__proto__
  //true

  ```

  上面代码中，`p1`和`p2`都是`Point`的实例，它们的原型都是`Point.prototype`，所以`__proto__`属性是相等的。

  这也意味着，可以通过实例的`__proto__`属性为“类”添加方法。

  > `__proto__` 并不是语言本身的特性，这是各大厂商具体实现时添加的私有属性，虽然目前很多现代浏览器的 JS 引擎中都提供了这个私有属性，但依旧不建议在生产中使用该属性，避免对环境产生依赖。生产环境中，我们可以使用 `Object.getPrototypeOf` 方法来获取实例对象的原型，然后再来为原型添加方法/属性。

  ```
  var p1 = new Point(2,3);
  var p2 = new Point(3,2);

  p1.__proto__.printName = function () { return 'Oops' };

  p1.printName() // "Oops"
  p2.printName() // "Oops"

  var p3 = new Point(4,2);
  p3.printName() // "Oops"

  ```

  上面代码在`p1`的原型上添加了一个`printName`方法，由于`p1`的原型就是`p2`的原型，因此`p2`也可以调用这个方法。而且，此后新建的实例`p3`也可以调用这个方法。这意味着，使用实例的`__proto__`属性改写原型，必须相当谨慎，不推荐使用，因为这会改变“类”的原始定义，影响到所有实例。

## Class表达式

与函数一样，类也可以使用表达式的形式定义。

```
const MyClass = class Me {
  getClassName() {
    return Me.name;
  }
};

```

上面代码使用表达式定义了一个类。需要注意的是，这个类的名字是`MyClass`而不是`Me`，`Me`只在 Class 的内部代码可用，指代当前类。

```
let inst = new MyClass();
inst.getClassName() // Me
Me.name // ReferenceError: Me is not defined

```

上面代码表示，`Me`只在 Class 内部有定义。

如果类的内部没用到的话，可以省略`Me`，也就是可以写成下面的形式。

```
const MyClass = class { /* ... */ };
```

## 不存在变量提升

类不存在变量提升（hoist），这一点与 ES5 完全不同。

```
new Foo(); // ReferenceError
class Foo {}
```
# 模块

ES6的模块自动采用严格模式，不管你有没有在模块头部加上“use strict"。

## export命令

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。

### export输出变量

```
export var firstName = 'Michael';
export var lastName = 'Jackson';
export var year = 1958;
```

或者

```
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};
```

优先使用第二种写法。

### export输出函数、类

```
export function multiply(x, y) {
  return x * y;
};
```

或者

```
function v1() { ... }
function v2() { ... }

export {
  v1 as streamV1,
  v2 as streamV2,
  v2 as streamLatestVersion
};
```

上面使用了`as`关键字重命名了函数v1，v2的对外接口。

### 注意

这样写是错的：

```
// 报错
export 1;

// 报错
var m = 1;
export m;
```

这样写才正确：

```
// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};
```

函数和类也一样

```
// 报错
function f() {}
export f;

// 正确
export function f() {};

// 正确
function f() {}
export {f};
```

## import命令

使用了`export`命令定义了模块对外的接口以后，其他js文件就可以通过`import`命令加载这个模块。

### import输入变量

```
import {firstName, lastName, year} from './profile';

function setName(element) {
  element.textContent = firstName + ' ' + lastName;
}
```

如果想为输入的变量重新取一个名字，`import`命令要使用`as`关键字，将输入的变量重命名。

```
import { lastName as surname } from './profile';
```

由于`import`是静态执行，所以不能使用表达式和变量，这些只有在运行时才能得到结果的语法结构。

```
// 报错
import { 'f' + 'oo' } from 'my_module';

// 报错
let module = 'my_module';
import { foo } from module;

// 报错
if (x === 1) {
  import { foo } from 'module1';
} else {
  import { foo } from 'module2';
}
```

最后，`import`语句会执行所加载的模块，因此可以有下面的写法。

```
import 'lodash';

```

上面代码仅仅执行`lodash`模块，但是不输入任何值。

## 模块的整体加载

除了指定加载某个输出值，还可以使用整体加载，即用星号（`*`）指定一个对象，所有输出值都加载在这个对象上面。

下面是一个`circle.js`文件，它输出两个方法`area`和`circumference`。

```
// circle.js

export function area(radius) {
  return Math.PI * radius * radius;
}

export function circumference(radius) {
  return 2 * Math.PI * radius;
}

```

现在，加载这个模块。

```
// main.js

import { area, circumference } from './circle';

console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));

```

上面写法是逐一指定要加载的方法，整体加载的写法如下。

```
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```

注意，模块整体加载所在的那个对象（上例是`circle`），应该是可以静态分析的，所以不允许运行时改变。下面的写法都是不允许的。

```
import * as circle from './circle';

// 下面两行都是不允许的
circle.foo = 'hello';
circle.area = function () {};
```