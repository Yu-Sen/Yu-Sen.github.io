---
title: Angular4——6.组件间通讯
date: 2017-12-22 14:18:15
tags:
- 学习笔记
categories:
- Angular
description: Angular组件间通讯
---

# 输入属性

**父组件**将数据传递给**子组件**。

在子组件中，被`@Input()`装饰器注解的属性，可以接收父组件的数据。

```
// 父组件.ts
export class fatherComponent{
  father:string = 'hello';
}
// 父组件.html
<div>我是父组件</div>
<app-child [son]="father"></app-child>

// 子组件.ts
export class ChildComponent{
  @Input() son:string;		// 'hello'
}
```

通过输入、输出属性传递参数的方式只能用在父子组件中。

# EventEmitter自定义事件

EventEmitter是RxJS中`subject`类的子类。

在响应式编程中，EventEmitter即可以作为观察者，也可以作为被观察者。

EventEmitter即可以通过`emit()`方法发射自定义事件，也可以通过`subscribe()`方法订阅所发射的事件流。

EvevtEmitter需要一个泛型，泛型是要发射的事件的类型。

# 输出属性

**子组件**传递数据给**父组件**。

在子组件中，被`@Output()`装饰器注解的属性，可以被父组件接收。

例：通过输出属性向外发射事件，并通过实践携带数据

```
// 子组件.ts
export class childComponent implements OnInit{
  @Output()
  sendData: EventEmitter<要发射的数据的类型> = new EventEmitter();
  
  ngOnInit(){
  	let data = '123';
    this.sendData.emit(data);
  }
}

// 父组件.html
<div>我是父组件</div>
<app-child (sendData)="getData($event)"></app-child>	//这里的$event就是子组件中emit()方法发射的数据

// 父组件.ts
export class fatherComponent{
  getData(data:数据类型){
    console.log(data)
  }
}

```

Output装饰器函数可以为输出属性重命名：

```
// child
@Output('newName')
sendData:EventEmitter<要发射的数据的类型> = new EventEmitter();

//father
<div>我是父组件</div>
<app-child (newName)="getData($event)></app-child>
```



# 中间人模式

由于输入输出属性只能用于父子关系的组件间传递数据。如果一个父组件下的两个子组件间要传递数据，可以将父组件当作中间人，通过中间人进行传递

```
// 子组件A 发射事件
export class AChildComponent{
  @Output()
  sendData: EventEmitter<string> = new EventEmitter();
  NgOnInit(){
  	let data:string = '123';
    this.sendData.emit(data)
  }
}

// 父组件 中间人，捕获子组件A发射的事件，并把数据输出给子组件B
<div>我是父组件 中间人</div>
<app-achild (sendData)="getData($event)">发送数据的组件A</app-achild>
<app-bchild [bValue]="value>接收数据的组件B</app-bchild>

export class father{
  value:string;
  getData(data:string){
    this.value = data
  }
}

// 子组件B 接收数据
export class BChildComponent{
  @Input()
  bValue:string;
}
```

当两个组件有共同的父组件时，可以使用父组件作为中间人进行通讯。

如果两个组件没有共同的父组件，应该使用一个可注入的服务作为中间人进行通讯。



# 组件生命周期

组件初始化：constructor（只调用一次），ngOnChanges，ngOnInit（只调用一次），ngDoCheck，ngAfterContentInit（只调用一次），ngAfterContentChecked，ngAfterViewInit（只调用一次），ngAfterViewChecked

变化检测：ngOnChanges，ngDoCheck，ngAfterContentChecked，ngAfterViewChecked

组件销毁：ngOnDestroy（只调用一次）

组件从创建到销毁依次调用以下生命周期钩子函数：

1. constructor

   当一个组件被创建时，首先调用构造函数方法。

   构造函数一定会被调用，但其他钩子函数是根据需要来调用的。

2. ngOnChanges

   ngOnChanges(changes: SimpleChange){ }

   这个钩子函数，是当一个父组件修改或初始化一个子组件的输入属性时才调用的。

   如果一个组件没有输入属性，ngOnChanges方法永远不会被调用。

   ngOnChanges首次调用一定发生在ngOnInit之前，而且这个方法可以被多次调用，每当输入属性变化时就会被调用。

3. ngOnInit

   如果组件存在输入属性，则ngOnInit方法在ngOnChanges首次调用之后被调用，用来初始化组件或指令。

   不在构造函数里初始化的原因：

   输入属性在构造函数里是空的，输入属性是在首次调用ngOnChanges时被初始化的。所以，如果组件的初始化逻辑依赖输入属性的值，那么初始化逻辑一定要卸载ngOnInit里。

4. ngDoCheck

   在每个angular变更检测周期中调用，用来检测

5. ngAfterContentInit

6. ngAfterContentChecked

7. ngAfterViewInit

8. ngAfterViewChecked

9. ngOnDestroy

## onChanges钩子

这个钩子是在父组件初始化或者修改子组件的输入属性时被调用。

**注意**：

+ 首先，ngOnChanges钩子只有在输入属性变化时才会被调用，组件自身的属性通过`[(ngModel)]`双向绑定的方式改变，不会调用ngOnChanges。

+ 其次，只有输入属性的值的类型是number、string这种不可变的原始类型时，输入属性改变时，ngOnChanges才会被调用；如果输入属性的值的类型是对象这种可变的复杂类型时，ngOnChanges不会被调用。

  ```
  //father
  greeting:string = "hello";
  <app-child [greeting]="greeting"></app-child>

  //child
  @Input()
  greeting:string;

  ngOnChanges(changes: SimpleChanges){
    console.log('OK')		// 'ok'		ngOnChanges被调用，输入属性也被初始化
  }
  <div>{{greeting}}</div>		// hello
  ```

+ 最后，虽然输入属性的值的类型为对象时，ngOnChanges不会被调用，但是输入属性依然能够正确初始化，这是因为angular的变更检测机制起了作用。

  ```
  //father
  user:{name:string} = {name: "tom"}
  <app-child [user]="user"></app-child>

  //child
  @Input()
  user:{name:string};
  ngOnChanges(changes: SimpleChanges){
    console.log('OK')		//	  ngOnChanges没有被调用，因为输入属性是一个对象，是一个可变类型
  }
  <div>{{user.name}}</div>		// tom 但是输入属性依然能偶被初始化
  ```



# 变更检测机制和DoCheck

## 变更检测机制

angular中实现变更检测机制的包是`zone.js`，它的作用是使组件的属性和页面同步变化。

浏览器的任何异步事件，如按钮点击，setTimeOut方法等，都会出发变更检测。

在angular1中，原生的事件无法触发脏检查，比如按钮click事件。而在aunglar2以后，原声事件随便用。

变更检测机制只是将属性的改变反映到模版上，变更检测机制本身永远不会改变属性的值。

### 变更检测原理

angular应用是一个以主组件为根的组件树，当angular应用运行时，每一个组件都会生成一个它自己的变更检测器。当任何一个变更检测器检测到变化时，`zone.js`就会根据组件的变更检测策略来检查组件，以判断组件是否需要更新模版。

###变更检测策略

angular实现了两个变更检测策略，一个是default，一个是OnPush。

默认情况下所有组件都是default策略。

如果所有的组件都是default策略，那么不管变更发生在哪个组件上，`zone.js`都会检查整个组件树。

`zone.js`从跟组件开始向下检查，当遇到是OnPush策略的组件时，就不会检查该组件及其以下的组件。

## DoCheck

```
ngDoCheck(){
  
}
```

鼠标的点击，键盘按键，都会出发变更检测，所谓的触发变更检测就是调用DoCheck钩子，因此在调用ngDoCheck的时候要小心使用。

# View钩子

## 父组件调用子组件的方法

父组件调用子组件的方法有两种，一种是在控制器中通过`@ViewChild()`装饰器来调用，另一种是在模版中通过模版本地变量调用。

```
// 子组件.ts
export class ChildComponent{
  greeting(name:string){
    console.log('hello'+name);
  }
}

// 父组件
<app-child #child1></app-child>		// 'hello Tom'
<app-child #child2></app-child>
<button (click)="child2.greeting('Jerry')">点我</button>		// 'hello Jerry'

export class fatherComponent implements OnInit{
  @ViewChild("child1")			//装饰器函数有一个参数，值就是模版中声明的模版本地变量
  child1:ChildComponent;
  
  ngOnInit(){
    this.child1.greeting("Tom");
  }
}
```

## ngAfterViewInit、ngAfterViewChecked

+ ngAfterViewInit的调用先于ngAfterViewChecked，这两个方法都是在组件的视图被组装完毕以后调用的。  

+ 如果一个组件有子组件，那么父组件要在所有子组件的视图都组装完毕以后，才会调用自己的ngAfterViewInit和ngAfterViewChecked方法。

+ ngAfterViewInit只会在视图初始化完毕后调用一次，之后永远不会再调用。

+ 在ngAfterViewInit、ngAfterViewChecked方法里面改变去改变组件属性的值，angular会抛出异常：

  ```
  <div>{{message}}</div>

  message:string;
  ngAfterViewInit(){
    this.message = 'hello';
  }

  // Error!!
  ```

  这是因为，在变更检测周期中，angular禁止在一个视图已经组装好之后，再去更新视图。而ngAfterViewInit是在视图已经组装好之后触发的。

  同样，ngAfterViewChecked方法也是在视图组装好之后触发的，所以在ngAfterViewChecked方法中去更新视图也会抛出异常。

  ```
  <div>{{message}}</div>

  message:string;
  ngAfterViewChecked(){
    this.message = 'hello';
  }

  // Error!!
  ```

  如果需要在ngAfterViewInit或者ngAfterViewChecked中改变视图，放在一个setTimeOut中就可以：

  ```html
  <div>{{message}}</div>

  message:string;
  ngAfterViewChecked(){
  	setTimeOut(() => {
        this.message = 'hello';
  	},0)
  }

  // hello
  ```

# ng-content指令

ng-content指令：父组件的内容投影到子组件中。

```html
// 父组件
<app-child>
	<div class="content1">内容1</div>
	<div class="content2">内容2</div>
</app-child>

//子组件
<div>
	<ng-content select=".content1"></ng-content>
	<div>
    	<ng-content select=".content2"></ng-content>  
  	</div>
</div>

```

# ngContent钩子

ngContent钩子和ngView钩子类似，不同的是ngContent钩子针对的是父组件投影到子组件的部分。

`ngAfterContentInit`是在投影进来的内容初始化完毕后调用的。

`ngAfterContentChecked`是针对投影进来的内通进行变更检测的。

+ 先完成投影内容的初始化和检测，再完成整个视图的初始化和检测。
+ 在`ngAfterContentInit`和`ngAfterContentChecked`中改变视图内容是不会报错的，因为在调用这两个钩子被调用的时候整个视图还没有被组装完。

# 组件生命周期

+ 组件初始化阶段：

  + 属性初始化阶段：

    1. constructor（只调用一次）：构造函数实例化对象
    2. ngOnChanges：初始化输入属性
    3. ngOnInit（只调用一次） ：初始化除了输入属性以外的所有其他属性。
    4. ngDoCheck：做一次变更检查

  + 这时整个组件的属性都应该被赋予了需要被赋予的值，这时开始渲染视图

  + 视图初始化阶段：

    ​	先渲染投影部分的内容，投影部分渲染完毕后调用下面两个钩子。

    5. ngAfterContentInit（只调用一次）：

    6. ngAfterContentChecked：

       这时如果组件有子组件，那么进入子组件的生命周期。

       如果没有子组件，或者所有子组件的生命周期执行完毕，这时如果组件的全部视图渲染完了，会调用下面两个方法：

    7. ngAfterViewInit（只调用一次）：

    8. ngAfterViewChecked：

当7，8方法调用完毕后，整个组件的初始化过程就完毕了这时候，组件就会呈现出来，给到用户做一些交互。

而用户做的任何一个交互，比如鼠标点击，键盘输入，都会出发angular的变更检测机制。

而一旦检测到发生了变更，那么在当前组件树上，所有活动 组件上被实现的带有`check`关键字的方法，都会被调用，用来检查当前组件的变化。

如果某个组件的变化导致输入属性也改变了，那么这个组件的ngOnChanges方法也会被调用。

+ 变更检测阶段：

  ngOnChanges

  ngDoCheck

  ngAfterContentChecked

  ngAfterViewChecked

+ 组件销毁

  最终当组件被销毁的时候，会调用ngOnDestroy。

  组件会在路由的时候被销毁。