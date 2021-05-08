---
title: Angular4——8.与服务器通讯
date: 2018-01-10 17:15:25
tags:
- Angular
categories:
- 学习笔记
description: angular与服务器通讯，http，websocket
---

# 使用NodeJS创建一个简单的web服务器

## 1.创建一个新的项目

## 2.使用npm init初始化项目

```
npm init -y
```

`-y`这个参数会生成一个包含默认配置的package.json文件。

## 3.引入Node类型定义文件

```
npm install @types/node --save
```

使用TypeScript开发node服务器，需要引入node的类型定义文件。

类型定义文件的作用是让开发者在TypeScript中可以使用JavaScript写成的已有的库。

## 4.配置编译TypeScript

node并不认识TypeScript语法，需要编译成JavaScript。

### 1.编写tsconfig.json配置文件

根目录下新建`tsconfig.json`文件，配置内容如下：

```
{
  "compilerOptions": {		// 编译器配置
    "target": "es5",			// 目标是编译成ES5
    "module": "commonjs",		// 现在使用的模块的规范是commonjs
    "emitDecoratorMetadata": true, //和装饰器相关，编译时保留装饰器元数据
    "experimentalDecorators": true,//和装饰器相关，编译时保留装饰器元数据
    "outDir": "build",			// 编译后的Js文件放在build目录下
    "lib": ["es6"]				// 开发时使用es6语法
  },
  "exclude": [				// 编译时排除node_modules文件
    "node_modules"
  ]
}
```

### 2.设置IDE

webstorm中，打开preference，找到TypeScript，勾选Enable TypeScript Compiler，use tsconfig.json。

## 5.编写服务器代码

新建一个文件，比如server.ts，编写服务器代码：

```
import * as http from 'http';  // 引入Node的Http模块

const server = http.createServer((resquest,response) => {
  response.end('Hello Node');
});

server.listen(8000);
```

保存后会自动编译，编译后的js文件在build文件夹中。

##6.启动服务器

启动编译后的js文件。

```
node build/server.js
```



# 使用express框架创建服务器

## 1.安装express框架

express框架可以简化node服务器的开发，减少手工编码量。

```
npm install express --save
```

## 2.安装express类型定义文件

和node一样，也需要安装express的类型定义文件，以便我们可以使用TypeScript文件来开发。

```
npm install @types/express --save
```

## 3.编写服务器代码

新建一个文件，来编写服务器代码：

```
import * as express from "express";

const app = express();

app.get('/', (req, res) => res.send('Hello Express'));
app.get('/stock', (req, res) => res.send('Hello Stock'));

const server = app.listen(8080, 'localhost', () => {
  console.log('服务启动');
})
```



# Nodemon 热更新node服务器

在node服务器启动以后，如果服务器的文件发生变化，node不会自动更新这些变化，需要重启服务器。

Nodemon组件可以实时监测代码，一旦代码发生变化，服务器就会自动重启，刷新浏览器就可看到变化。

```
npm install nodemon --save
```



# 使用Angular提供的Http服务创建客户端

异步的http请求，可以使用多种方式实现。比如callback回调函数，promise，响应式编程。

angular的http服务默认使用响应式编程的方式来处理http请求。

##http服务

http类方法返回的是Observable可观察对象。

```javascript
class Http {
  constructor(_backend: ConnectionBackend, _defaultOptions: RequestOptions)
  protected _backend: ConnectionBackend
  protected _defaultOptions: RequestOptions
  request(url: string|Request, options?: RequestOptionsArgs): Observable<Response>
  get(url: string, options?: RequestOptionsArgs): Observable<Response>
  post(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>
  put(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>
  delete(url: string, options?: RequestOptionsArgs): Observable<Response>
  patch(url: string, body: any, options?: RequestOptionsArgs): Observable<Response>
  head(url: string, options?: RequestOptionsArgs): Observable<Response>
  options(url: string, options?: RequestOptionsArgs): Observable<Response>
}
```

## 创建客户端

```
// app.module.ts
@NgModule({
  imports:[
    HttpModule
  ]
})
```

```javascript
// stock.component.ts
import { Component, OnInit } from '@angular/core';
import {Headers, Http} from "@angular/http";
import "rxjs/Rx";
import {Observable} from "rxjs";

@Component({
  selector: 'app-stock',
  templateUrl: './stock.component.html',
  styleUrls: ['./stock.component.css']
})
export class StockComponent implements OnInit {

  stocks:Observable<any>;

  constructor(public http: Http) {

    let myHeaders:Headers = new Headers();
    myHeaders.append("Authorization", "Basic 123456");

    this.stocks = this.http.get('/api/stock', {headers: myHeaders}).map(response => response.json());
  }

  ngOnInit() {

  }

}

```

