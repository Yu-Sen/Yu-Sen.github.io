---
title: Angular4——7.表单处理
date: 2017-12-26 16:19:44
tags:
- Angular
categories:
- 学习笔记
description: Angular表单处理模块
---

# Angular表单

在Angular中存在两种表单处理方式：

## 模版驱动式表单

表单的数据模型是通过组件模版中的相关指令来定义的。由于使用这种方式定义表单的数据模型时，我们会受限于HTML的语法，所以，模版驱动方式只适用于一些简单的场景。

## 响应式表单

使用响应式表单时，通过编写TypeScript代码而不是Html代码来创建一个底层的数据模型，在定义好这个模型以后，使用一些特定的指令，将模版上的HTML元素与底层的数据模型连接在一起。

## Angular表单API

不管是哪种表单，都有一个对应的数据模型来存储表单的数据。在模版式表单中，数据模型是由angular基于组件模版中的指令隐式创建的。而在响应式表单中，你通过编码明确的创建数据模型然后将模版上的html元素与底层的数据模型连接起来。

数据模型并不是一个任意的对象，它是一个由angular/forms模块中的一些特定的类，如FormControl，FormGroup，FormArray等组成的。在模版式表单中，是不能直接访问这些类的。

响应式表单并不会替你生成HTML，模版仍然需要你自己来编写。



# 模版式表单

使用模版式表单时，需要引入`FormsModule`，只能使用指令定义数据模型。这些指令都来自于`FormsModule`模块。

```javascript
// app.module.ts
import {FormsModule} from '@angular/forms';
@NgModule({
  imports:[
    FormsModule
  ]
})
```

##模版式表单的指令：

### NgForm

ngForm指令代表整个表单，在Angular应用里`ngForm`指令会自动的添加到每一个`<form>`表单上。`ngForm`会隐式的创建一个`FormGroup`类的实例，这个类用来代表表单的数据模型，并且存储表单的数据。

```html
<form>
  <div>昵称：<input type="text"></div>
  <button type="submit">提交</button>
</form>
```

+ Angular应用会自动为`<form>`元素添加`ngForm`指令。

+ `ngForm`会自动拦截表单的提交事件，阻止表单的提交。Angular用一个自定义的`ngSubmit`事件来代替他。

  ```html
  <form (ngSubmit)="test()">
    <div>昵称：<input type="text"></div>
    <button type="submit">提交</button>
  </form>
  ```

+ `ngForm`会自动发现添加了`ngModel`的子元素，并将这些子元素的值会被添加到表单数据模型中。

+ `ngForm`指令可以也可以在其他元素上使用，比如`<div ngForm>`，这和写一个`<form>`效果是一样的。

+ 如果你不希望Angular来自动处理你的表单，你可以在`<form>`元素上明确的添加`ngNoForm`指令：

  ```html
  <form ngNoForm>...</form>
  ```

  添加了`ngNoForm`指令，angular将不再接管`<form>`表单的处理。

+ `ngForm`指令创建的对象可以被一个模版本地变量引用，以便在模版中访问ngForm对象的实例。

  value是一个JS对象，保存着form表单中所有字段的值。

  ```html
  <form #myForm="ngForm">
    <div>昵称：<input ngModel name="nickName" type="text"></div>
  </form>
  <div>{{myForm.value}}</div>

  //昵称：Tom
  //{nickName: Tom}
  ```

###NgModel

 `ngModel`指令代表表单中的一个字段，`ngModel`指令会隐式的创建一个`FormControl`类的实例，来代表字段的数据模型，并用这个`FormControl`的对象来存储字段的值。

```html
<form #myForm="ngForm">
  <div>昵称：<input ngModel name="nickName" type="text"></div>
</form>
<div>{{myForm.value}}</div>
```

+ 在`<form>`标签或者标记了`ngForm`指令的HTML元素内，使用`ngModel`指令时，不需要像双向绑定那样用`[()]`扩起来，应为这里不是双向绑定，而是表单字段的指令；也不需要绑定到组件的属性上。

  但是`ngModel`指令需要为添加它的HTML元素指定`name`的值。如果不指定`name`属性的值，angular不知道以什么名字把这个字段的值添加到数据模型中。

+ 与`ngForm`类似，`ngModel`指令创建的对象也可以被模版本地变量引用，并通过模版本地变量来访问这个对象的值。

  ```html
  <form #myForm="ngForm">
    <div>昵称：<input #myNickName="ngModel" ngModel name="nickName" type="text"></div>
  </form>
  <div>{{myForm.value}}</div>
  <div>昵称的值是：{{myNickName.value}}</div>
  ```

###NgModelGroup

`ngModelGroup`指令代表表单的一部分，他将表单的一部分组织在一起，形成更清晰的层级关系。

与`ngForm`类似，`ngModelGroup`也会创建一个`FormGroup`类型的对象。

```html
<form #myForm="ngForm">
  <div>昵称：<input ngModel name="nickName" type="text"></div>
  <div ngModelGroup="passwordInfo">
    <div>密码：<input type="password"></div>
    <div>确认密码：<input type="password"></div>
  </div>
</form>
<div>{{myForm.value}}</div>

//
{
  "nickName":"",
  "password":{}
}
```

#响应式表单

创建响应式表单与模版式表单不同，需要两步：

+ 首先通过编码创建一个数据模型。
+ 然后使用指令将模版中的HTML元素连接到数据模型上。

使用响应式表单时，需要引入`ReactiveFormsModule`模块，响应式表单的指令都来自`ReactiveFormsModule`模块。

```javascript
// app.module.ts
import {ReactiveFormsModule} from '@angular/forms';
@NgModule({
  imports:[
    ReactiveFormsModule
  ]
})
```

## 数据模型

数据模型是指一个用来保存表单数据的数据结构，他由定义在`FormsModule`模块中的三个类组成：

+ FormControl：

  他是构成表单的基本单位，通常情况下用来表示一个`<input>`元素，也可以用来表示更复杂的UI组件。

  FormControl类的对象保存着与其关联的HTML元素当前的值，元素是否被修改过，以及校验状态等信息。

  ```javascript
  // ReactiveForm.component.ts
  private nickname = new FormControl('tom');
  ```

  `FormControl`接收的参数用来制定初始值。

  模版式表单中，`ngModel`指令默认会为其附着的元素创建一个`FormControl`类的对象。

  ​

+ FormGroup：

  `FormGroup`类代表表单的一部分，也可以表示整个表单，他是多个`FormControl`的集合。

  `FormGroup`将多个`FormControl`的状态和值聚合在一起。如果其中一个`FormControl`的值是无效的，也就是不符合校验规则，那么整个`FormGroup`都是无效的。

  ```javascript
  // ReactiveForm.component.ts
  private passwordInfo = new FormGroup({
    password: new FormControl(),
    passwordConfirm: new FormControl()
  })
  ```

  `FormGroup`的构造函数需要传入一个对象`{}`，这个对象里是`FormControl`。

  ​

+ FormArray：

  `FormArray`和`FormGroup`是类似的，但是有一个额外的属性，长度，因为`FormArray`是一个数组。

  `FormGroup`用来代表整个表单，或者一个表单中固定的子集。

  `FormArray`用来代表一个可以增长的集合。比如，一个表单中的邮箱字段，一个用户可以拥有多个邮箱。

  ```javascript
  // ReactiveForm.component.ts
  private emails = new FormArray([
    new FormControl(),
    new FormControl(),
    new FormControl()
  ])
  ```

  `FormArray`中的`FormControl`没有key，而`FormGroup`中的`FormControl`是要有一个key。

编写一个完整的表单数据模型：

```javascript
// ReactiveForm.component.ts
export class ReactiveFormComponent{
  private formModel: FormGroup;
  constructor(){
    this.formModel = new FormGroup({
      nickname: new FormControl(),
      mobile: new FormControl(),
      emails: new FormArray([
        new FormControl()
      ]),
      passwordInfo: new FormGroup({
        password: new FormControl(),
        passwordConfirm: new FormControl()
      })
    })
  }
}
```

##响应式表单的指令：

在控制器中已经构建好了数据模型，在模版中通过指令，将表单元素和数据模型连接起来。

响应式表单使用和模版式表单完全不同的指令，这些指令来自于`ReactiveFormsModule`模块。

响应式表单的指令分成两种，一种是需要使用属性绑定语法`[xxx]="xxx"`的指令，另一种是不需要属性绑定语法的指令。

响应式表单的所有指令，都是以`form`开头的；而模版式表单的所有指令，都是以`ng`开头的。

###使用属性绑定的指令：

`formGroup`，`formControl`

这种指令使用时必须使用属性绑定语法，就是说使用这些指令时，需要使用属性绑定的语法，用`[]`扩起来。

属性绑定的指令，必须绑定到组件中的某个属性上，比如： 

```html
<form [formGroup]="formModel"></form>

export class ReactiveForm {
  private formModel: FormGroup;
}
```

指令的名字很简单，和类名一样，首字母小写。

`FormArray`类没有属性绑定方式的指令，因为它是数组，没有key来对应。

###不使用属性绑定的指令：

`formGroupName`，`formControlName`，`formArrayName`

这些以Name结尾的指令可以使用属性的名字来连接DOM元素和数据模型，它们不需要使用属性绑定语法。

`formGroup`通过属性绑定将表单绑定到组件中的`formModel`属性上，`nickname`不是组件的属性，只是数据模型中的一个字段的key，因此不能用属性绑定`[formControl]="nickname"`，否则会报错。

```javascript
<form [formGroup]="formModel">
	<input formControlName="nickname">
</form>

export class ReactiveForm {
  private formModel: FormGroup;
  constructor(){
  this.formModel = new FormGroup({
   nickname: new FormControl()
		})
	}
}
```

与模版式表单不同，响应式表单的指令都是不可引用的，不能像模版式表单那样用模版本地变量来引用。

而在模版式表单中，不能在控制器中直接访问`FormGroup`、`FormControl`这些类。

Angular是故意这么做，目的就是区分模版式表单和响应式表单。

```html
<form [formGroup]="formModel" (submit)="createUser()">
  <div>昵称：<input formControlName="nickname" type="text"></div>
  <div>邮箱：
  	<ul formArrayName="emails">
      <li *ngFor="let email of formModel.get('emails').controls; let i = index">
      	<input [formControlName]="i">
      </li>
    </ul>
  </div>
  <div>手机号：<input formControlName="mobile" type="text"></div>
  <div formGroupName="passwordInfo">
    <div>密码：<input formControlName="password" type="password"></div>
    <div>确认密码：<input formControlName="passwordConfirm" type="password"></div>
  </div>
  <button type="submit">注册</button>
</form>
```

```javascript
export class ReactiveForm {
    private formModel: FormGroup;
	constructor(){
      this.formModel = new FormGroup({
        nickname: new FormControl(),
        mobile: new FormControl(),
        emails: new FormArray([
          new FormControl()
        ]),
        passwordInfo: new FormGroup({
          password: new FormControl(),
          passwordConfirm: new FormControl()
        })
      })
  	}
	createUser(){
        
    }
}
```

**注意**：

+ 处理submit事件：
  + 模版式表单中提供了`ngSubmit`事件来处理表单提交：`<form #myForm="ngForm"(ngSubmit)="createUser(myForm)"`。在模版式表单中，通过把保存表单数据的模版本地变量传给控制器来获取表单数据。
  + 响应式表单中直接处理原生的`submit`事件：`<form (submit)="createUser()">`。并且，不需要传模版本地变量，因为响应式表单中本身就不能引用模版本地变量，表单的数据模型是在组件控制器中定义的，在控制器中就能直接拿到数据。

## FormBuilder

FormBuilder是Angular提供的一个工具类，它并没有提供新的功能，只是简化构建响应式表单数据结构的语法。

```javascript
export class ReactiveForm{
  private formModel: FormGroup;
  private fb: FormBuilder = new FormBuilder();
  constructor(){
    this.formModel = this.fb.group({
        nickname: [''],
        mobile: [''],
        emails: this.fb.array([
          [''],
        ]),
        passwordInfo: this.fb.group({
          password: [''],
          passwordConfirm: [''],
        })
      })
  }
}
```

+ `new FormGroup({}) = new FormBuilder().group({});`
+ `new FormControl() = new FormBuilder().[''];`
+ `new FormArray([]) = new FormBuilder().array([]);`

此外，FormBuilder还可以对表单模型进行其他的配置，比如校验表单。

# 表单校验

## Angular校验器

angular校验器有两种，一个是自己定义的校验器，另一个是angular预定义好的校验器：

### Angular预定义的校验器

angular提供的校验器都在一个叫`Validators`的类中，这个类有很多方法，比如：

+ `Validators.required`必填的校验器
+ `Validators.minLength()`最小长度的校验器
+ `Validators.maxLength()`最到长度的校验器

等等。

当有了定义好的校验器，包括angular提供的或者自己定义的，我们就可以配置数据模型来使用校验器。

 将校验器作为参数传到表单模型的构造函数中就能进行校验：

```
this.formModel = this.fb.group({
  nickname:['',Validators.required]
})
```

这个意思是nickname这个字段是必填的。

也可以同时传入多个校验器，那么这个参数就是一个数组：

```
this.formModel = this.fb.group({
  nickname: ['', [Validators.required, Validators.minLength(5)]
})
```

nickname这个字段是必填的，同时最小长度是5。

###自定义校验器

自定义的校验器实际上就是一个自定义的方法，该方法要求需要传入一个`AbstractControl`类的参数，并且该方法的返回值必须是`key`为`string`类型的对象：

```
xxx(param: AbstractControl):{[key:string]: any}{
  return null;
}
```

+ `AbstractControl`类是`FormGroup`，`FormControl`，`FormArray`的父类，因此，自定义校验器的参数可以是这三个类中的任意一种。

当需要更复杂的校验规则时，angular提供的校验器可能无法满足需求，我们可以通过自定义的检验器来实现。

## 响应式表单校验

```javascript
class FormBuilder {
  group(controlsConfig: {[key: string]: any}, extra: {[key: string]: any}|null = null): FormGroup
  
  control(formState: Object, validator?: ValidatorFn|ValidatorFn[]|null, asyncValidator?: AsyncValidatorFn|AsyncValidatorFn[]|null): FormControl
  
  array(controlsConfig: any[], validator?: ValidatorFn|null, asyncValidator?: AsyncValidatorFn|null): FormArray
}
```

```javascript
import {FormControl, FormGroup} from "@angular/forms";
import {Observable} from "rxjs";

export function mobileValidator(mobile: FormControl):any {
  let value = (mobile.value || '') + '';
  var myreg = /^(((13[0-9]{1})|(15[0-9]{1})|(18[0-9]{1}))+\d{8})$/;
  let valid = myreg.test(value);
  console.log('mobile是否校验通过:'+valid);
  return valid ? null : {mobile:true};
}

export function mobileAsyncValidator(mobile: FormControl):any {
  let value = (mobile.value || '') + '';
  var myreg = /^(((13[0-9]{1})|(15[0-9]{1})|(18[0-9]{1}))+\d{8})$/;
  let valid = myreg.test(value);
  console.log('mobile是否校验通过:'+valid);
  return Observable.of(valid ? null : {mobile:true}).delay(5000);
}

export function passwordValidator(info: FormGroup):any {
  let password:FormControl = info.get('password') as FormControl;
  let pConfirm:FormControl = info.get('passwordConfirm') as FormControl;
  if(password != null && pConfirm != null) {
    let valid:boolean = password.value === pConfirm.value;
    console.log('password是否校验通过:'+valid);
    return valid ? null : {password: {description:'密码和确认密码不匹配'}};
  }
  return null;
}
```

```javascript
export class ReactivedFormComponent implements OnInit {

  private formModel:FormGroup;

  private fb:FormBuilder = new FormBuilder();

  constructor() {
    this.formModel = this.fb.group({
      nickname: ['xxxx', [Validators.required, Validators.minLength(6)]],
      emails: this.fb.array([
        ['']
      ]),
      mobile: ['', mobileValidator, mobileAsyncValidator],
      passwordInfo: this.fb.group({
        password: ['', Validators.required],
        passwordConfirm: ['']
      }, {validator: passwordValidator})
    })
  }
}
```

```html
<form [formGroup]="formModel" (submit)="createUser()" >
  <div>昵称:<input [class.inputHasError]="formModel.get('nickname').invalid" formControlName="nickname" type="text" pattern="[a-zA-A0-9]+"></div>
  <div [hidden]="!formModel.hasError('required','nickname')">
    昵称是必填项
  </div>
  <div [hidden]="!formModel.hasError('minlength','nickname')">
    昵称的最小长度是6
  </div>
  <div>邮箱:
    <ul formArrayName="emails">
      <li *ngFor="let email of formModel.get('emails').controls;let i = index">
        <input [formControlName]="i">
      </li>
    </ul>
    <button type="button" (click)="addEmail()">增加Email</button>
  </div>
  <div>手机号:<input formControlName="mobile"  type="number"></div>
  <div [hidden]="formModel.get('mobile').valid || formModel.get('mobile').pristine">
    <div [hidden]="!formModel.hasError('mobile','mobile')">
      手机号不合法
    </div>
  </div>
  <div [hidden]="!formModel.get('mobile').pending">
    正在校验手机号合法性
  </div>
  <div formGroupName="passwordInfo">
    <div>密码:<input formControlName="password" type="password"></div>
    <div [hidden]="formModel.get('passwordInfo.password').valid || formModel.get('passwordInfo.password').untouched">
      <div [hidden]="!formModel.hasError('required','passwordInfo.password')">
        密码是必填项
      </div>
    </div>
    <div>确认密码:<input formControlName="passwordConfirm" type="password"></div>
  </div>
  <div [hidden]="!formModel.hasError('password','passwordInfo')">
    {{formModel.getError('password', 'passwordInfo')?.description}}
  </div>
  <button type="submit">注册</button>
</form>
<div>
  {{formModel.status}}
</div>

```

+ `FormGroup.hasError('校验器的key','被校验字段的key')`
+ `FormGroup.getError('校验器的key','被校验字段的key')`
+ `FormGroup.get('').`

