---
title: Angular4——1.介绍Angular4
date: 2017-10-04 17:49:26
tags:
- 学习笔记
categories:
- Angular
description: Angular4学习+项目实战笔记
---

# Angular介绍

angular是有Google维护的开源JavaScript框架。

目前有两个大的版本，1.5和4.0。

angular4并不是在1的基础上升级而来，而是一个完全重写的版本。

 angular1统一叫做**AngularJS**，angular2以后统一叫做**Angular**，这也是官方的叫法。

## AngularJS的问题

**性能**

随着组件的增加，双向数据绑定会带来性能问题。AngularJS通过dirty checking脏检查来跟踪数据的改变来动态更新用户界面 ，在AngularJS的作用域中任何的操作都会引发dirty checking，随着绑定数量的增加性能就会越来越低。

在Angular中，这种检查机制被完全废弃，默认的数据绑定方式也由双向绑定改为了单项绑定，整个检查机制也完全重写，整个性能得到很大提升。

**路由**

在AngularJS中  ，用来做路由的指令`ng-View`只能有一个，不能嵌套多个视图，虽然有第三方模块ui-router可以解决路由问题，但是 UI-router对URL的控制也不是很灵活，它必须是一个嵌套模式的URL。

在Angular中，路由模块作为整个框架的核心模块之一，进行了巨大的提升。

**作用域**

在AngularJS中，有个重要的作用域机制，这个作用域把angular的执行环境和浏览器的环境隔离开，所有原生的事件都不能影响angular的作用域，你无法响应任何原生事件。**比如**，你不能用按钮的click事件，只能用ng-click指令。如果想用原生的事件，必须调用$scope.$apply()来告诉AngularJS我的数据更新了。

在Angular中，$scope不复存在，任何原生的事件都可以被处理

**表单验证**

AngularJS中，显示错误信息这部分很薄弱，需要写很多模版标签

在Angular中，提供了一套全新的表单API，尤其是一种叫响应式表单

**JavaScript**

angularJS中，存在JavaScript语言本身的问题，比如他没有类型系统，面向对象开发的能力不够

在Angular中，使用TypeScript语言开发，他是ES6点超级，带有完善的类型系统

**学习成本**

angularJS需要学习大量的概念，模块、控制器、指令、作用域、模版、数据绑定、链式函数、过滤器、依赖注入......=_=

Angular的核心就是组件，其他的东西都是为组件服务

## Angular的新特性

全新的命令行工具 Angular Cli

服务器端渲染

移动和桌面的兼容

官方UI库