---
title: Angular4——4.依赖注入
date: 2017-12-19 14:50:09
tags:
- Angular
categories:
- 学习笔记
description: Angular依赖注入
---

# 注入器

每一个组件都有一个注入器实例， 负责注入组件需要的对象。注入器是Angular提供的一个服务类，一般情况下不需要直接调用注入器的方法，注入器会自动通过组件的构造函数，将组件所需的对象注入进组件：

```
construncot(private productService: ProductService){...}
```

# 注入器的层级关系

应用级注入器 -> 住组件注入器 -> 子组件注入器

子组件的构造函数中注入了一个服务，angular会先查找这个子组件自己是否声明了提供器，如果没有，就再向上查找主组件是否声明，如果还没有再向上查找应用级，也就是根模块中是否声明。

# 提供器

为了让注入器知道被注入的对象如何实例化，我们需要提供器。

通过组件活着模块的`providers`属性来声明提供器：

```
providers:[ProductService]
providers:[{provide: ProductService, useclass: ProductService}]
providers:[{provide: ProductService, useclass: AnotherProductService}]
```

provide属性指明了提供器的token，这个token就是组件构造函数中的注入器的类型。

useclass属性指明了提供器具体要实例化的类。

如果注入器的类型和要实例化的类的类型一样，可以简写成`providers:[ProductService]`。

如果用相同的token注入，但是实例化不同的类，要在服务中实现相同的token：

```
export class AnotherProductService implements ProductSercice{
  ...
}
```

一个服务实现另一个服务，必须实现另一个服务的所有方法。

# 提供器优先级

提供器`providers`可以声明在根模块，也可以声明在组件。

当一个`providers`声明在根模块这一级的时候，它对所有的组件可见，所有组件都可以注入。

当一个`providers`声明在组件这一级的时候，它只对声明它对组件以及自组件可见，其他组件不能注入。

当声明在模块中的提供器和声明在组件中的提供器使用同样的token时，声明在组件中的提供器会覆盖声明在模块中的提供器。

一般情况下，我们应该把服务声明在模块中。

# @Injectable

服务类中的`@Injectable()`装饰器表示这个服务类可以通过构造函数注入其他服务，而不是表示这个服务可以被注入。

这个服务能否被注入，是由模块或组件中是否声明`providers`提供器决定的。

# 工厂函数提供器

```
providers: [{ provide: ProductService, useFactory: () => {
  if(true){
    return new ProductService()
  }else{
    return new AnotherProductService()
  }
}}]
```

工厂函数提供器，可以把其他提供器作为参数传进来使用，deps属性的值时一个数组，作用是为useFactory属性的匿名函数提供参数。

```
providers:[
	{
	  provide: ProductService, useFactory: (a:XXXX, b:AnotherService) => {
  		console.log(a)	// {isTrue: true}
  		console.log(b)
	  },deps:["XXXXX",AnotherService]
	},{
      provide: 'XXXXX', useValue:{isTrue: true}
	},{
      provide: AnotherService, useFactory()=>{}
	}]
```

# 值提供器

```
providers:[{ provide: 'ValueProvider', useValue: true}]
```

