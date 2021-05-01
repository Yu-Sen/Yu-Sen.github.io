---
title: Angular4——3.路由导航
date: 2017-12-09 15:21:37
tags:
- 学习笔记
categories:
- Angular
description: 使用Angular Router模块路由导航
---

# 基础知识

硬件领域中的路由器是用来帮你找到另一台网络设备的，而Angular的**路由器**能让用户从一个[视图](https://angular.cn/guide/glossary#view)导航到另一个视图。

浏览器具有我们熟悉的导航模式：

- 在地址栏输入URL，浏览器就会导航到相应的页面。
- 在页面中点击链接，浏览器就会导航到一个新页面。
- 点击浏览器的前进和后退按钮，浏览器就会在你的浏览历史中向前或向后导航。

Angular的`Router`（即“路由器”）借鉴了这个模型。它把浏览器中的URL看做一个操作指南， 据此导航到一个由客户端生成的视图，并可以把参数传给支撑视图的相应组件，帮它决定具体该展现哪些内容。 我们可以为页面中的链接绑定一个路由，这样，当用户点击链接时，就会导航到应用中相应的视图。

## \< basehref \>元素

路由器使用浏览器的[history.pushState](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Adding_and_modifying_history_entries)进行导航。我们必须往本应用的`index.html`中**添加一个\< base href \> 元素**，这样`pushState`才能正常工作。 当引用CSS文件、脚本和图片时，浏览器会用`<base href>`的值作为*相对*URL的前缀。

```
// index.html
<head>
	// ...
	<base href="/">
</head>
```

## 导入路由器

Angular的路由器是一个可选的服务，它用来呈现指定的URL所对应的视图。 它并不是Angular核心库的一部分，而是在它自己的`@angular/router`包中。 像其它Angular包一样，我们可以从它导入所需的一切。

```
// app.module.ts
import { RouterModule, Routes } from '@angular/router';
```



# 路由基础

## Routes路由数组

type [Routes](https://angular.cn/api/router/Routes) = [Route](https://angular.cn/api/router/Route)[];

```
interface Route { 
  path?: string
  pathMatch?: string
  matcher?: UrlMatcher
  component?: Type<any>
  redirectTo?: string
  outlet?: string
  canActivate?: any[]
  canActivateChild?: any[]
  canDeactivate?: any[]
  canLoad?: any[]
  data?: Data
  resolve?: ResolveData
  children?: Routes
  loadChildren?: LoadChildren
  runGuardsAndResolvers?: RunGuardsAndResolvers
}
```

## RouterModule路由器模块

```
class RouterModule {
  constructor(guard: any, router: Router)
  static forRoot(routes: Routes, config?: ExtraOptions): ModuleWithProviders
  static forChild(routes: Routes): ModuleWithProviders
}

```

## RouterOutlet 路由出口

指令，用在html模版中，用来指明路由所匹配的组件应该展示在什么位置。

```
// app.component.html
<body>
	<a [routerLink]="[{ outlets: { popup: ['compose'] } }]">Contact</a>
	<router-outlet></router-outlet>
	<router-outlet name="popup"></router-outlet>
</body>
```

一个模版中只能有一个未命名的\<router-outlet>，但可以有多个命名的出口。

```
// app-routing.module.ts
{
  path: 'compose',
  component: ComposeMessageComponent,
  outlet: 'popup'
},
```

## RouterLink 路由链接

指令。用在html模版中，用于导航路由。

```
<a routerLink="/heroes" routerLinkActive="active">Heroes</a>
```

routerLinkActive指令，绑定css样式。

## Router路由器

对象，用在控制器中，用于路由导航。和routerlink功能一样，用来控制路由导航。

```
// app.module.ts
import {Router} from '@angular/router';

export class AppComponent{
  constructor(private router: Router){}
  toHeroes(){
    this.router.navigate(['/heroes']);
  }
}
```

router.navigate方法等参数和routerLink的参数一样，也是个数组。

```
class Router {
  constructor(rootComponentType: Type<any>|null, urlSerializer: UrlSerializer, rootContexts: ChildrenOutletContexts, location: Location, injector: Injector, loader: NgModuleFactoryLoader, compiler: Compiler, config: Routes)
  events: Observable<Event>
  routerState: RouterState
  errorHandler: ErrorHandler
  navigated: boolean
  urlHandlingStrategy: UrlHandlingStrategy
  routeReuseStrategy: RouteReuseStrategy
  config: Routes
  initialNavigation(): void
  setUpLocationChangeListener(): void
  get url(): string
  resetConfig(config: Routes): void
  ngOnDestroy(): void
  dispose(): void
  createUrlTree(commands: any[], navigationExtras: NavigationExtras = {}): UrlTree
  navigateByUrl(url: string|UrlTree, extras: NavigationExtras = {skipLocationChange: false}): Promise<boolean>
  navigate(commands: any[], extras: NavigationExtras = {skipLocationChange: false}): Promise<boolean>
  serializeUrl(url: UrlTree): string
  parseUrl(url: string): UrlTree
  isActive(url: string|UrlTree, exact: boolean): boolean
}
```

## ActivatedRoute激活的路由

对象。保存当前激活的路由信息，如路由地址，路由参数等

```
interface ActivatedRoute { 
  snapshot: ActivatedRouteSnapshot
  url: Observable<UrlSegment[]>
  params: Observable<Params>
  queryParams: Observable<Params>
  fragment: Observable<string>
  data: Observable<Data>
  outlet: string
  component: Type<any>|string|null
  get routeConfig(): Route|null
  get root(): ActivatedRoute
  get parent(): ActivatedRoute|null
  get firstChild(): ActivatedRoute|null
  get children(): ActivatedRoute[]
  get pathFromRoot(): ActivatedRoute[]
  get paramMap(): Observable<ParamMap>
  get queryParamMap(): Observable<ParamMap>
  toString(): string
}
```



# 配置路由

## 配置在app.module.ts文件中

```
// app.module.ts
import {NgModule} from '@angular/core';
import {RouterModule, Routes} from '@angular/router';

import {AppComponent} from 'app.component';
import {AComponent} from 'a.component';
import {BComponent} from 'b.component';

const appRoutes: Routes = [
  {path: 'aaa', component: AComponent},
  {path: 'bbb', component: BComponent}
]

@NgModule({
  imports:[
    RouterModule.forRoot(appRoutes)
  ],
  declarations:[
    AppComponent,
    AComponent,
    BComponent
  ]
})
export class AppModule{}
```

## 单独配置一个路由模块

随着应用的成长，我们将需要将路由配置重构到单独的文件，并创建**路由模块** - 一种特别的、专门为特性模块的路由器服务的**服务模块**。

在/app目录下创建一个app-routing.module.ts的路由配置文件。

```
// src/app/approuting.module.ts
import {NgModule} from '@angular/core';
import {RouterModule, Routes} from '@angular/router';

import {AComponent} from 'a.component';
import {BComponent} from 'b.component';

const appRoutes: Routes = [
  {path: 'aaa', component: AComponent},
  {path: 'bbb', component: BComponent}
]

@NgModule({
  imports:[
    RouterModule.forRoot(appRoutes)
  ],
  export:[
    RouterModule
  ]
})
export class AppRoutingModule{}
```

```
// app.module.ts
import {AppRoutingModule} from 'approuting.module';

import {AppComponent} from 'app.component';
import {AComponent} from 'a.component';
import {BComponent} from 'b.component';

@NgModule({
  import:[
    AppRoutingModule
  ],
  delarations:[
    AppComponent,
    AComponent,
    BComponent
  ]
})
```

在根模块中，RouterModule调用forRoot方法，在其他子模块中，RouterModule调用forChild方法。

##重定向路由

```
{path: '', redirectTo: '/heroes', pathMatch: 'full'}
```

pathMatch： 描述匹配策略的属性，值为两个字符串，full和prefix

prefix：默认值，只要URL的开始是以path开头的，就进行重定向路由。由于' '空字符串是所有url的前缀，所以不管输入什么都会重定向。

full：输入的url必须和path完全一致，才会进行重定向。

##通配符路由

```
{path: '**', component: ....}
```

可以匹配任何路由，放在路由数组的最后，优先匹配原则。通常用来导航到自定义的404页面。



# 路由导航

## 使用RouterLink导航

```
<a routerLink="/hero"></a>

{path:'hero',component:HeroComponent}
```

## 使用Router导航

```
<button (click)="toHero()"></button>

constructor(private router:Router){}
toHero(){
  this.router.navigate(['/hero'])
}
```



# 路由传递数据

## 在查询参数中传递数据

/product?id=1&name=2   =>   ActivatedRoute.quaryParams[id]

```
<a routerLink="/hero" [queryParams]="{id:1}"></a>

constructor(private routeInfo: ActivatedRoute){}
ngOnInit(){
  this.heroId = this.routeInfo.snapshot.queryParams["id"];
}
```

## 在路由路径中传递数据

{path: /product/:id}  =>  /product/1  =>  ActivatedRoute.params[id]

```
{path:'hero/:id', component: HeroComponent}

<a routerLink="['/hero',hero.id]"></a>
<button (click)="toHero()"></button>

constructor(private routeInfo: ActivatedRoute, private router: Router){}
ngOnInit(){
  this.heroId = this.routeInfo.snapshot.params["id"];
  //
  this.routeInfo.params.subscribe((params:Params) => this.heroId = params["id"])
}
toHero(){
  this.router.navigate(['/hero'],2)
}
```

## 在路由配置中传递数据

{path: /product, component: ProductComponent, data:[{isProd: true}]}  =>  ActivatedRoute.data\[0][isProd]

```
{path: 'hero', component: HeroComponent, data:[{isPro:true}]}

constructor(private routeInfo: ActivatedRoute){}
ngOnInit(){
  this.isPro = this.routeInfo.snapshot.data[0]["isPro"];
}
```



# 子路由

```
{
	path:'xxx',
	component: XXXX,
	children:[
      path: 'YYY',
      component: YYY
	]
}

<a routerLink="./children1"></a>
```

根路由用`/`开头

子路由用`./`开头

上级路由用`../`开头



# 辅助路由

```
<router-outlet></router-outlet>
<router-outlet name="aux"></router-outlet>

{path: 'xxxx', component:AuxComponent, outlet: 'aux'}

<a [routerLink]="[{outlets:{aux: 'xxxx'}}]"></a>
<a [routerLink]="[{outlets:{aux: null}}]"></a>
```



# 路由守卫

## CanActivate

处理导航到某路由的情况。

一 写路由守卫

```
//guard.ts
import {CanActivate} from '@angular/router';

export class GuardName implements CanActivate{
  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot){
    return true; // 返回true，就允许进入导航，返回false就拒绝
  }
}
```

二 把守卫配置到路由上

```
//app.module.ts
import {GuardName} from 'guard';

{ path: 'xxxxx', component: XXXX, canActivate: [GuardName]}

@NgModule({
  providers:[GuardName]
})
```

## CanDeactivate

处理从当前路由离开的情况。

一 写路由守卫

```
//guard.ts
import {CanDeactivate} from '@angular/router';
import {XXXComponent} from 'xxx';

export class GuardName implements CanDeactivate<XXXComponent>{	//泛型是要守卫的组件
	// canDeactivate方法需要把要保护的组件作为参数传进来
  canDeactivate(component: XXXComponent, route: ActivatedRouteSnapshot, state: RouterStateSnapshot){
      return component.isTrue; // 返回true，就允许离开导航，返回false就拒绝
  }
}
```

二 把守卫配置到路由上

```
//app.module.ts
import {GuardName} from 'guard';

{ path: 'xxxxx', component: XXXX, canDeactivate: [GuardName]}

@NgModule({
  providers:[GuardName]
})
```

## Resolve

在路由之前获取路由数据。

一 写路由守卫

```
// guard.ts
import {Resolve} from '@angular/router';
impot {XXX} from 'xxx';

export class GuardName implements Resolve<XXX>{	// 泛型是要获取的数据的类型
  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot){
    return route.params["id"];
  }
}
```

二 把守卫配置到路由上

```
//app.module.ts
import {GuardName} from 'guard';

{ path: '....', component: ComponentName, resolve: {xxx: GuardName}}

@NgModule({
  providers:[GuardName]
})
```

三 在目标组件中接收数据

```
//Component
ngOnInit(){
  this.activatedRoute.data.subscribe((data:{stock: Stock}) => {
    this.stock = data.stock;
  })
}
```

