---
title: Angular4——5.数据绑定、响应式编程和管道
date: 2017-12-19 17:10:05
tags:
- Angular
categories:
- 学习笔记
description: Angular数据绑定、响应式编程、管道
---

# 数据绑定

angularJS默认使用双向绑定，angular默认使用单向绑定

## 插值表达式、属性绑定

属性表达式和插值表达式是一个东西。

```
<img [src]="url">
//
<img src="{{url}}">
```

**Angular的模版绑定是通过DOM属性和事件来工作的，而不是HTML属性。**

### DOM属性绑定

插值表达式是DOM属性绑定。

Html属性和DOM属性的区别：

```
<input value="Tom" (change)="inputChange($event)">

inputChange(event:any){
  console.log(event.target.value);			//这是DOM属性，value会随着输入的值而变化。DOM属性表示当前的值，DOM属性的值可以改变
  console.log(event.target.getAttribute('value'));	//这是Html属性，value不变。Html属性指定初始值，html属性的值不能改变
}
```

### HTML属性绑定

1. 基本html属性绑定

   ```
   <td [attr.colspan]="tableColspan">Something</td>
   ```

2. css类绑定

   ```
   // [class]绑定的样式会替换class的样式，[class]等号右边的表达式的值字符串，内容是要绑定的样式名称
   <div class="aaa bbb" [class]="someExpression">Something</div>

   //[class.样式名称]不会覆盖已有的样式，等号右边是一个boolean值，为true则绑定样式
   <div class="a b" [class.special]="isSpecial">Something</div>

   //[ngClass]管理多个样式，等号右边是一个对象，key是样式名，值是表达式，返回true则绑定
   <div [ngClass]="{aaa:isA, bbb:isB}"></div>
   ```

3. 样式绑定

   ```
   <button [style.color]="isRed?'red':'blue'"></button>

   <button [ngStyle]="{'color':isRed?'red':'green'}</button>
   ```

### 什么时候使用HTML属性绑定

应当优先使用DOM属性绑定，没有DOM属性的时候，使用HTML属性绑定。

例如：

表格的colspan属性是HTML属性，DOM属性中没有这个属性，如果使用DOM属性绑定，也就是插值表达式绑定该属性的话，浏览器会报错：

```
<table>
	<tr><td colspan="{{2}}">Hello world</td></tr>
</table>
```

由于td的colspan属性不是DOM属性，使用DOM属性绑定当然就会报错。

改成HTML属性绑定就正确了：

```
<table>
	<tr><td [attr.colspan]="2">Hello world</td></tr>
</table>
```

## 事件绑定

```
<button (click)="onButtonClick($event)">
```

`()`表示这是一个angular的事件绑定；

`(click)`括号中的内容是事件的名称，这里是一个click事件

`=`等号右侧是事件表达式，当绑定的事件触发时，右侧的事件表达式会被执行。表达式是组件控制器中的一个方法；

`$event`是一个浏览器事件对象。如果处理事件的方法需要拿到事件本身这个对象。

```
<button (click)="save = true">
```

`=`等号右侧的表达式不用必须是一个函数调用，也可以是一个赋值表达式。

被绑定的事件不一定必须是标准的DOM事件，也可以是一个自定义事件。

## 双向绑定

```
<input [(ngModel)]="name">{{name}}</input>
```



# 响应式编程

## 观察者模式与RxJS

[RxJS中文文档](https://buctwbzs.gitbooks.io/rxjs/content/observer.html)。

观察者模式由可观察对象和观察者对象两个对象组成。

一般在初始化可观察对象时，会向可观察对象中注册一些观察者对象。

当可观察对象发生变化时，会调用观察者对象中相应的方法，来处理这些变化。

这种模式就叫观察者模式。

```
var subscription = Observable.from([1,2,3,4])//Observable.from方法从数组[1,2,3,4]中创建一个流
					.filter((e) => e%2==0) //调用filter方法过滤这个流，把偶数选出来
					.map((e) => e*e)	//调用map方法映射这个流，求偶数的平方
					.subscribe(	//经过上面操作，此时拿到了一个包含偶数平方的流，订阅这个流，打印出来
						e => console.log(e),	// 4 16
						error => console.error(error),
						() => console.log("结束啦“)
					);
```

+ 可观察对象Observable（流）：表示一组值或事件的集合。可观察对象也叫流，因为它会像水流一样不停的依次发送值。

  本例中1，2，3，4这四个值组成了一个可观察对象。也可以用事件生成一个可观察对象：

  ```
  var button = document.querySelector('button');
  Observable.from(button,'click')
  ```

+ 观察者对象Observer：一个回调函数的集合，它知道怎样去监听被Observable发送的值。

  在本例中`.subscribe()`方法的参数是三个函数，这三个函数组成的回调函数集合就是可观察对象`[1,2,3,4]`的观察者。当可观察对象发射出`1`这个值的时候，这个值就会被交给观察者的第一个回调函数处理`console.log(1)`。

+ 订阅Subscription：表示一个可观察对象，主要用于取消注册。

  当一个可观察对象调用`subscribe()`方法，也就是被订阅以后，会返回一个可观察对象，本例中就是`subscription`。这个可观察对象可以调用`unsubscribe()`方法取消订阅：

  ```
  subscription.unsubscribe();
  ```

  观察者里有三个回调函数，分别表示`next`、`error`、`complete`。

  ```
  var observer = {
    next: x => console.log('Observer got a next value: ' + x),
    error: err => console.error('Observer got an error: ' + err),
    complete: () => console.log('Observer got a complete notification'),
  };
  observable.subscribe(observer);
  ```

  后两个可以省略。

+ 操作符 Operators：纯粹的函数，使开发者可以以函数编程的方式处理集合。

  在本例中，`filter()`、`map()`都是操作符，它们可以处理流中的数据。

Observable对象是Rxjs库提供的，在angular中使用Observable进行响应式编程时需要引入RxJS库：

```
import {Observable} from 'rxjs'
```

## 模版本地变量

angular中提供了一个叫做*模版本地变量*的语法来方便获得Html元素。

```
//事件对象
<input (keyup)="onKey($event)">
onKey(event:any){
  console.log(event.target.value)
}

//模版本地变量
<input #myFiled (keyup)="myFiled.value>
onKey(value:string){
  console.log(value);
}
```

声明模版本地变量用`#`号，`#myFiled`。

使用模版本地变量时不需要加`#`号，`myFiled`就是input标签。`myFiled.value`就能直接拿到input元素的值。

## 使用响应式编程处理DOM事件

传统JS中，对DOM事件的处理是一次性的。例如click事件，每点击一次鼠标，所对应的方法被调用一次。

在Angular中，将事件作为永不结束的流来处理。

### 1.引入ReactiveFormsModule模块

```
// app.module.ts
import {ReactiveFormsModule} from '@angular/forms';

@NgModule({
  imports:[ReavtiveFormsModule]
})
```

这个模块是angular中用来做响应式编程的模块。

### 2.使用FormControl类绑定Html元素

FormControl是angular进行表单处理时常用的类。

```
// ex.html
<input [formControl]="searchInput" (keyup)="onKey()">

// ex.component.ts
export class ExComponent implements OnInit{
  searchInput: FormControl = new FormControl();
  constructor(){
    this.searchInput.valueChanges.debounceTime(500).subscribe(
    	e => console.log(e)
    )
  }
}
```

valueChanges是一个Observable可观察对象，每当被控制的元素的值发生变化，valueChanges就会发射一个流。

debounceTime是Observable可观察对象的方法，作用是延迟。该方法不属于angular，来自RxJS库的Observable对象，因此使用该方法要引入RxJS库。



# 管道

```
// ts
birthday:Date = new Date();

// html
<div>我的生日是：{{birthday}}</div>

// 我的生日是：Thu Dec 21 2017 15:01:10 GMT+0800 (CST)
```

使用管道格式化数据：

```
// html
<div>我的生日是：{{birthday | date}}</div>

// 我的生日是：Dec 21,2017
```

多个管道可以组合使用：

```
// html
<div>我的生日是：{{birthday | date | uppercase}}</div>

// 我的生日是：DEC 21,2017
```

angular内置了一些管道，如date，uppercase，lowercase等。

一些复杂的管道可以接受参数，比如date管道：

```
// html
<div>我的生日是：{{birthday | date:'yyyy-MM-dd HH:mm:ss'}}</div>

// 我的生日是：2017-12-21 16:00:00
```

date管道接受一个参数，该参数指定当前的日期格式化的形式，yyyy表示年（2017），大写的MM表示月（12），dd表示日（21），大写的HH表示24小时制的时（16），相反小写的hh表示12小时制的时（04），小写的mm表示分（00），ss表示秒（00）。

## 自定义管道

一 生成管道文件

`ng g pipe XXXX`

管道需要声明在模块中

```
// app.module.ts
@NgModule({
  declaration:[XXXXPipe]
})
```

```
// pipeName.pipe.ts
import {Pipe,PipeTransform} from '@angular/core';

@Pipe({
  name: 'XXXX'
})
export class XXXXPipe implements PipeTransform{
  transform(value: any, arg?: any): any{
    return undefined
  }
}
```

value参数是在模版中，传入管道的原始值。

arg参数是管道可接受的参数，比如date管道接受的`'yyyy-MM-dd HH:mm:ss'`。

管道可以接收多个参数：

```
transform(value:any, arg?;any, arg2?:any...):any{
  return undefined
}

<div>{{value | pipe:arg:arg2:....}}</div> 
```

例子：写一个乘法管道

```
// multiply.pipe.ts
@Pipe({
  name:'multiply'
})
export class MultiplyPipe implements PipeTransform{
  transform(value:any, arg?:any):any{
    if(!arg){
      arg = 1;
    }
    return value * arg;
  }
}

// ex.component.html
// 假设num的值为 5
<div>{{num | multiply}}</div>	// 5
<div>{{num | nultiply:'2'}}</div>	// 10
```