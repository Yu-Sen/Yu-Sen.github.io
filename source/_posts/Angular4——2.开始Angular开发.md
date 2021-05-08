---
title: Angular4——2.开始Angular开发
date: 2017-10-06 15:50:21
tags:
- Angular
categories:
- 学习笔记
description: Angular框架理解，Angular开发环境搭建（node,IDE,angular-cli），Angular项目结构解析
---

# Angular开发环境搭建

+ 安装nodejs

+ 安装IDE

+ 安装Angular-cli

  `npm install -g @angular/cli`

  推荐使用cnpm安装，npm不稳定，node-sass包被墙极容易导致安装报错

  安装cnpm：

  `$ npm install -g cnpm --registry=https://registry.npm.taobao.org`

+ 使用`ng -v`查看安装结果

+ 使用`ng new <name>`创建项目

  使用ng new初始化项目时，npm安装包也会出错，这里用`ng new <name> —skip-install`跳过自动安装，初始化完成后，再用cnpm安装:

  ```
  ng new projectname --skip-install
  cd progjectname
  cnpm install
  ```

**在引用安装好的依赖包时，注意cnpm安装的路径名**

# Angular项目结构

**命令行工具是按照一定的规则来生成这些目录和文件的，如果改变这些文件的位置或者重命名文件，有可能导致命令行工具其他的功能出错。所以，除非你明确的知道你在干什么，否则不要改这些文件**

```
Project/
|__ e2e（端到端的测试目录，包含基本的测试装，用来做自动测试的）
|__ src/（应用源代码目录，我们写的所有代码都应该在这里面）
|	|__ app/（包含应用的组件和模块，我们要写的代码绝大部分都是在这个目录下）
|	|	|__ app.component.css
|	|	|__ app.component.html
|	|	|__ app.component.spec.ts
|	|	|__ app.component.ts（组件）
|	|	|__ app.module.ts（模块）
|	|__ assets（空的文件夹，用来存放静态资源，比如图片）
|	|__ environments（环境配置）
|	|__ favicon.ico
|	|__ index.html（这是整个应用的根html，应用启动第一次访问的就是这个文件，body里有一个<app-root>）
|	|__ main.ts（整个应用的脚本执行入口文件，angular通过这个文件启动整个项目）
|	|__ polyfills.ts（用来导入一些库，使angular可以运行在老版本的浏览器）
|	|__ style.css（放应用的全局的css样式）
|	|__ test.ts（用来自动化测试的文件）
|	|__ tsconfig.ts（typescript编译器的配置）
|__ .editorconfig（webstorm的配置文件，与项目无关）
|__ .gitignore（git的配置文件，与项目无关）
|__ .angular-cli.json（angular命令行工具的配置文件，引一些第三方的包就是通过修改这个文件来实现的）
|__ karma.conf.json（karma是单元测试执行器，这是karma的配置文件，他是用来执行自动化测试的）
|__ package.json（标准的npm工具配置文件，该文件中列明了这个应用所使用的第三方依赖包。在创建项目的时候会有一个installing packages的提示信息，实际上在这里安装包的时候，他就是根据package.json中的内容去下载相应的第三方依赖包，下载下来以后会放到node_modules这个目录中）
|__ protractor.conf.json（和karma.conf.json类似，也是做自动化测试的）
|__ README.md（标准的README文件，包含Angular命令行工具生成项目的标准说明）
|__ tsconfig.json
|__ tslint.json（tslint的配置文件，他是用来定义TypeScript代码质量检查的规则的）
```

# Angular框架结构

模型（ngModul）

组件（component）

服务（service）

# Angular组件相关概念

## 必备元素

1. 组件元数据装饰器（@component()）

   简称装饰器，装饰器用来告知angular框架如何处理一个typescript类。

   装饰器包含多个属性，这些属性的值叫做元数据。angular会根据这些元数据的值来渲染组件 并执行组件的逻辑

2. 模版（Template）

   通过组件自带的模版，来定义组件的外观。

   模版以html的形式存在，告诉angular如何渲染组件。

   我们可以在模版中使用angular的数据绑定语法，来呈现控制器中的数据

3. 控制器（Controller）

   控制器就是一个普通的typescript类，他会被装饰器装饰。

   控制器包含组件所有的属性和方法，绝大多数的页面逻辑都是写在控制器里的。

   控制器通过数据绑定，与模版通讯。模版展现控制器里的数据，控制器处理模版上发生的事件