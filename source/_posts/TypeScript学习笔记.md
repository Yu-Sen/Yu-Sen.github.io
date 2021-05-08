---
title: TypeScript学习笔记
date: 2017-10-03 16:26:54
tags:
- typeScript
categories:
- 学习笔记
description: typescript语法简介
---

# TypeScript介绍

微软开发的脚本语言

javascript的超集

遵循ES6规范

2016年9月发布的Angular2框架就是由TypeScript编写的

# TypeScript的开发环境

搭建typescript的开发环境，就是安装typescript的编译器（compiler）。

编译器的作用是把typescript的代码转换成javascript代码

为什么

因为ES6规范是2015年发布的，目前大多主流的浏览器并没有完全支持ES6规范。因此用ES6写的代码不能直接放到浏览器里跑，需要转换成ES5

**两种编译器**

在线compiler：[typescript在线编译器](http://www.typescriptlang.org/play/index.html)

本地compiler：`sudo npm install -g typescript`

# TypeScript语法

## 字符串新特性

### 多行字符串

ES5:

```
//传统javascript中字符串换行必须用+号拼接起来
var content = "aaa"+
"bbb"+
"ccc";
```

TS:

```
//TS多行字符串可以随意换行，用``双撇号声明
var content = 'aaa
bbb
ccc`;
```

### 字符串模版

在一个**多行字符串里**，用一个变量插入表达式、调用方法 

 ```
`...${}...`
 ```

ts：

```
var name = "Ethan"
var getName = function () {
    return "Ethan";
}

console.log(`<div>
<span>${name}</span>
<soan>${getName()}</span>
</div>`)
```

编译后：

```
var name = "Ethan";
var getName = function () {
    return "Ethan";
};
console.log("<div>\n<span>" + name + "</span>\n<soan>" + getName() + "</span>\n</div>");
```

**注意**：表达式的语法只在多行字符串里起作用，如果这个字符串是双引号""声明的普通字符串，表达式不会被解析，双引号之间的所有内容都会被作为字符串打印出来，就像这样：

```
//ts
console.log("hellow ${name}")

//js
console.log("hellow ${name}");
```

### 自动拆分字符串

当用一个字符串模版调用一个方法的时候，这个字符串模版里面表达式的值，会自动赋给被调用方法的参数。

ts:

```
var test = function (template, name, age) {
    console.log(template);
    console.log(name);
    console.log(age);
}

var myName = "Ethan";
var getAge = function () {
    return 25;
}

test`hello, my name is ${myName},i'm ${getAge()}`;
```

如果要使用自动拆分的特性，在调方法的时候不要写圆括号`test()`，而是直接写字符串模版``test`hello, my name is ${myName},i'm ${getAge()}`;``

编译后：

```
var test = function (template, name, age) {
    console.log(template);
    console.log(name);
    console.log(age);
};
var myName = "Ethan";
var getAge = function () {
    return 25;
};
(_a = ["hello, my name is ", ",i'm ", ""], _a.raw = ["hello, my name is ", ",i'm ", ""], test(_a, myName, getAge()));
var _a;
```

结果：

```
VM2163:2 Array(3)
0: "hello, my name is "
1: ",i'm "
2: ""
raw: (3) ["hello, my name is ", ",i'm ", ""]
length: 3__proto__: Array(0)
VM2163:3 Ethan
VM2163:4 25
```

用一个字符串模版去调用test方法，字符串模版里表达式的值会赋给相应的参数。

第一个参数template就是字符串模版的值，第二个参数name就是第一个表达式的值，第三个参数age就是第二个表达式的值

## 参数新特性

### 参数类型

 在参数名称后面，可以使用冒号：来指定参数的类型；

变量指定类型、函数返回值指定类型、函数参数指定类型、自定义类型

#### 变量指定类型

```
//ts
var myName: string = "Ethan";
var age: number = 12;
var man: boolean = true;
var a: any = "123";

//js
var myName = "Ethan";
var age = 12;
var man = true;
var a = "123";
```

声明了指定类型变量的作用，是在以后再次给变量赋值时，编译器会根据指定类型做检查，如果类型不一致编译器会报错，以此来降低开发人员出错的几率。但是编译后的js不会报错，因为ES5中变量为弱类型，就想这样：

```
//ts
var myName: string = "ethan";  //声明了一个字符串类型的指定类型变量

myName = 13; //再次赋值number，ts中会报错，但仍可以编译

//js
var myName = "ethan";
myName = 13; //js中正常，不会报错

```

如果我想声明一个可以是任意类型的变量，就用any：

```
//ts
var a: any = "ethan"
a = 13; //不会报错
```

如果在ts中，不指定任何类型生命变量

```
//ts
var myName = "Ethan";
```

再次给这个变量赋值，赋一个数字，就会报错。这是因为ts中存在类型推断机制，这个机制会在第一次给变量赋值的时候推断变量类型 ：

```
//ts
myName = 13  //报错
```

#### 函数返回值指定类型

在方法后声明指定类型，指定这个方法的返回值的类型，比如指定一个方法返回string类型的值：

```
//ts
function test(): string{
  return "Ethan"	//return返回的值必须是string类型，否则报错
}
```

用void声明一个方法，表明这个方法不需要任何返回值，指定返回值会报错

```
//ts
function test(): void {
    return "323"  //报错
 }
```

#### 函数参数指定类型

给方法的参数指定类型：

```
//ts
function test(name: string):string{
  return ""
}

test("Ethan")
```

在调用方法的时候，参数只能是string类型，如果用其他类型就会报错

#### 自定义类型

 通过**class**关键字 或 接口自定义一个类型，比如自定义一个Person类型：

```
//ts
class Person{
    name: string;
    age: number;
}
//编译后
var Person = /** @class */ (function () {
    function Person() {
    }
    return Person;
}());
```

下面我就可以声明一个自定义的Person类型：

```
//ts
class Person{
    name: string;
    age:number;
}

var ethan: Person = new Person();
ethan.name = "Ethan";
ethan.age = "18";
```

### 默认参数

在参数声明后面用等号声明参数的默认值。

```
var myName: string = "Ethan"
```

**给方法的变量指定默认值**

```
function test(a: string,b: string,c: string = "Ethan"){
  console.log(a);
  console.log(b);
  console.log(c);
}

test("xxx","yyy")

//console.log
xxx
yyy
Ethan
```

**注意**

带默认值的参数一定是声明在最后面，如果声明在前面，像这样：

```
function test(a: string = "Ethan",b: string,c: string){
  console.log(a);
  console.log(b);
  console.log(c);
}
test("xxx","yyy")
```

调用test方法就会报错，因为xxx是参数a，yyy是参数b，c参数没有传。TS中调用方法时不传参数会报错。

### 可选参数

在方法的参数声明后面用?来标明此参数为可选参数

```
function test(a: string,b?: string,c: string = "Ethan"){
  console.log(a);
  console.log(b);
  console.log(c);
}

test("xxx")

//console.log
xxx				//a参数传了"xxx"
undefined		//b参数是可选参数，没传
Ethan			//c参数有默认值
```

**注意**

1.需要处理可选参数没传时候的情况，可选参数没传,这个参数就是undefined，如果方法里调用这个参数(undefined)就会报错

2.比传参数不能在可选参数后面：

```
function test(a?: string,b: string,c: string = "Ethan"){
  console.log(a);
  console.log(b);
  console.log(c);
}
```

b是必选参数，在可选参数a的后面，这样写会报错

## 函数新特性

### Rest and Spread操作符

…就是Rest and Spread操作符

**用法一**：用来声明任意数量参数的方法

```
//ts
function fun1(...args) {
    args.forEach(function (arg) {
        console.log(arg)
    })
}

fun1(1, 2, 3);			//输出1，2，3
fun1(7, 8, 9, 10, 11);	//输出7，8，9，10，11

//编译后
function fun1() {
    var args = [];
    for (var _i = 0; _i < arguments.length; _i++) {
        args[_i] = arguments[_i];
    }
    args.forEach(function (arg) {
        console.log(arg);
    });
}
fun1(1, 2, 3);
fun1(7, 8, 9, 10, 11);
```

**用法二**：方法参数数量固定，调用方法的时候传入任意数量参数

该语法是ES6的，目前版本的TypeScript还不支持，但是编译后的js是可以正常运行的

```
//ts
function fun1(a,b,c) {
    console.log(a);
    console.log(b);
    console.log(c);
}

var arg1 = [1, 2];
var arg2 = [7, 8, 9];

fun1(...arg1);		//输出1，2，undefined
fun1(...arg2);		//输出7，8，9

//编译后
function fun1(a, b, c) {
    console.log(a);
    console.log(b);
    console.log(c);
}
var arg1 = [1, 2];
var arg2 = [7, 8, 9];
fun1.apply(void 0, arg1);
fun1.apply(void 0, arg2);
```

### Generator函数

控制函数的执行过程，手动暂停和恢复代码执行

在ES5中，调用一个方法，没有办法让这个方法执行到一半停住。但是在ES6里，新添加了一个关键字yield，这个关键字可以实现这个特性

这个特性目前版本TypeScript还不支持，他是ES6的语法

声明一个generator函数非常简单，就是在function后面加*号，像这样

```
//ts
function* doSomething(){
  console.log("start");
  yield;		//相当于一个断点
  console.log("finish");
}
```

在调用generator函数时，如果像往常一样调用函数是不会起任何作用的：

`doSomething()`

控制台不会有任何输出

调用generator函数，必须把它声明成一个变量，然后调用这个变量的next()方法：

```
var func = doSomething();
func.next();
```

每调一次next方法，generator函数就会执行到yield关键字停住，然后接着调用next方法，函数继续执行到下一个yield，如此继续直到函数执行结束。

### 析构表达式

析构表达式的作用是通过表达式将对象或数组拆解成任意数量的变量

析构表达式的好处就是，当你需要用一个对象的属性或者一个数组的元素来初始化一些本地的变量时，可以写更少的代码

#### 用析构表达式从对象里取值

```
//ts
function getStock(){
  return{
    code:"IBM",
    price:{
      price1:200,
      price2:400
    },
    aaa:"xixi",
    bbb:"haha"
  }
}
var {code: codex, price:{price2}} = getStock();		//对象的属性转化成本地的两个变量

console.log(codex);		//IBM
console.log(price2);	//400
```

+ 针对对象的析构表达式，用花括号{ }声明

+ { }里面的变量名要和被析构的对象的属性名一致

+ 如果想让表达式中的变量梦和被解构对象的属性名不一致，要这么写

  `var {code: codex} = getStock();`

  表示本地声明一个codex变量，并把getStock对象的code属性值赋给他

+ 如果被析构对象的属性值也是对象，想解构嵌套对象的属性这么写：

  `var {price: {price2}} = getStock();`

  表示声明一个price2变量，获取getStock对象中price对象的price2属性

+ 对象里的其他属性不会影响析构表达式的执行

#### 用析构表达式从数组里取值

```
var array = [1, 2, 3, 4, 5];

var [a, b] = array;		//1,2

var [a, , , b] = array;	//1,4

var [a, b, ...c] = array;//1,2,Array[3,4,5]

function doSomething([a, b, ...c]){
  	console.log(a);
	console.log(b);
	console.log(c);
}
doSomething(array);
```

+ 针对数组的析构表达式用方括号[ ]声明
+ 表达式中用,,跳过不需要析构的数组元素
+ 用Rset操作符取出剩余的是一个数组，包含所有剩余的元素
+ 方法的形参也可以是一个针对数组的析构表达式，调用方法的传参会按照形参的析高表达式被析构

## 表达式和循环

### 箭头表达式

用来声明匿名函数，消除传统匿名函数的this指针问题

```
//ts
var sum1 = (arg1, arg2) => arg1 + arg2;		//函数体只有一行，不用写{} return
var sum2 = (arg1, arg2) => {				//函数体多行，要写{} return
    return arg1 + arg2;
}
var sum3 = () => { };			//函数没有参数，不用写
var sum4 = arg => {				//函数只有一个参数，不用写()
    return arg
}

//js
var sum1 = function (arg1, arg2) { return arg1 + arg2; };
var sum2 = function (arg1, arg2) {
    return arg1 + arg2;
};
var sum3 = function () { };
var sum4 = function (arg) {
    return arg;
};
```

**关于this指针的例子**

```
//es5
function getStock(name: string) {
    this.name = name;

    setInterval(function () {		//es5写法
        console.log(this.name)		//es5中，匿名函数的调用者是window对象，this指向函数的调用者。
        							因此，this.name为空
    })
}

var stock =new  getStock("IBM");	//空

//es6
function getStock2(name: string) {
    this.name = name;

    setInterval(() => {				//es6写法
        console.log(this.name)
    })
}

var stock =new  getStock2("IBM");	//IBM
```

### for of循环

#### 循环数组

```
var array = ['a', 'b', 'c', 'd']
array.desc = "number array";

//es5
array.forEach(function (value) {
    console.log(value);			//a,b,c,d
})
for (var index in array) {
    console.log(index)			//0,1,2,3,desc
}

//es6
for (var el of array) {
	if(el=='c') break;			//a,b
    console.log(el)				//a,b,c,d
}
```

+ forEach方法只循环数组的元素，不循环数组的属性，不能跳出循环
+ for in方法循环数组的所有元素下标以及属性键名，不能跳出循环
+ for of方法只循环数组的元素，可以用break跳出循环

#### 还可以循环字符串

```
for (var n of array.desc) {
    console.log(n)			//n,u,m,b,e,r, ,a,r,r,a,y
}
```

## 面向对象新特性

