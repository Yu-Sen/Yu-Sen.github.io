---
title: ES5学习笔记
date: 2017-06-01 12:52:28
tags:
- 学习笔记
- ES5
categories:
- JavaScript
description: 《JavaScript教程-阮一峰》《JavaScript高级程序设计》ES5要点总结
---

# 语法

## 基本语法

+ 凡是JavaScript语言中预期为值的地方，都可以使用表达式。

+ 使用var重新声明一个已经存在的变量是无效的，但是如果第二次声明的同时还附了值，则会覆盖掉前面的值。

  ```
  var x = 1;
  var x;
  x // 1

  var x = 1;
  var x = 2;
  x // 2
  ```

+ JavaScript引擎的工作方式是，现解析代码，获取所有被声明的变量，然后再一行一行的运行。这造成的结果就是所有的变量声明语句，都会被提升到代码的头部，这就叫做变量提升。

  ```
  console.log(a);
  var a = 1;
  //undefined
  //等同于
  var a;
  console.log(a);
  a = 1;
  ```

  变量提升只对var命令声明的变量有效，如果一个变量不是用var命令声明的，就不会发生变量提升。

  ```
  console.log(b);
  b = 1;
  //Error:b is not defined
  ```

+ 中文是合法的标识符，可以用作变量名

  ```
  var 临时变量 = 1；
  ```

+ 多个if...else连在一起使用的时候，可以转为使用更方便的switch结构

  ```
  switch (fruit){
    case "banana":
    	//...
    	break;
    case "apple":
    	//...
    	break;
    default:
    	//...
  }
  ```

  每个case代码块内部的break语句不能少，否则会接下去执行下一个case代码块，而不是跳出switch结构。

  switch语句部分和case语句部分，都可以使用表达式

  switch语句后面的表达式与case语句后面的表达式，在比较运算结果时，采用严格相等===，而不是相等运算==，意味着比较时不会发生类型转换

  ```
  var x = 1;
  switch(x){
    case true:
    	console.log('x发生类型转换‘);
    default:
    	console.log('x没有发生类型转换‘);
  }
  // x没有发生类型转换
  ```

+ while循环、for循环可以使用break语句跳出循环

+ continue用语立即终止本轮循环，返回循环结构头部，开始下一轮循环

+ 如果存在多重循环，不带参数的break和continue语句都只针对最内层循环

+ 标签（label）：JS允许语句的前面有标签，相当于定位赋，用于跳转到程序的任意位置，格式如下：

  ```
  label:
  	statement
  ```

  标签可以时任意的标识符，但不能是保留字

  标签通常和break、continue语句配合使用，跳出特定的循环：

  ```
  top:
  	for(var i = 0; i < 3; i++){
        for(var j = 0; j < 3; j++){
          if(i ===1 && j === 1){
            break top;
          }
        }
  	}
  ```

  上面的双重循环中，break后面跟了top标签（top不用加引号），满足条件时，直接跳出双层循环。如果不加标签，则只跳出内层循环，进入下一次外层循环。

  continue一样

## 数据类型

+ 6中数据类型（es6又新增了第七中Symbol类型），数值、字符串、布尔值称为原始类型，对象称为合成类型，null、undefined特殊值

+ JS的所有数据，都可视为广义的对象，数组、函数，原始类型的数据

+ 对象又分成3个子类：狭义的对象（object），数组（array），函数（function）

+ JS把函数当成一种数据类型，可以像其他类型的数据一样，进行赋值和传递，体现了JS作为“函数式语言”的本质

+ JS有三种方法确定一个值的类型：

  typeof运算符

  instanceof运算符

  Object.prototype.toString方法

+ typeof null返回object，并不是说null的数据类型就是对象，本质上null是一个类似于undefined的特殊值。null表示空值，即该处的值为空；undefined表示“为定义”

+ 空数组 []，空对象 {}对应的布尔值都是true

### 数值

+ NaN是number类型的一种特殊值，表示“非数字”（Not a Number)

+ isNaN方法判断一个值是否为NaN，空数组和只有一个数值成员的数组，is NaN返回false，原因是这些数组能被Number函数转成数值。

  ```
  isNan([]) //false
  isNaN([123]) //false
  isNaN(['123']) //false
  ```

+ parseInt()方法用于将字符串转为整数	。

  字符串转为整数是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分

  ```
  parseInt('12.34') //12
  ```

  如果字符串的第一个字符不能转化为数字（后面跟着数字的正负号除外），返回NaN：

  ```
  parseInt('abc') //NaN
  parseInt('+') //NaN
  parseInt('+1') //1
  ```

  parseInt()方法还可以接受第二个参数（2到36之间），表示被解析到值的进制，返回该值对应的十进制数。第二个参数默认是10。

+ parseFloat()方法用于将一个字符串转为浮点数

  如果字符串包含不能转为浮点数的字符，则不再进行往后转换，返回已经转好的部分

  如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回NaN

### 字符串

+ 如果要在单引号（或双引号）字符串的内部，使用单引号（或双引号），就必须在内部的单引号（或双引号）前面加上反斜杠，用来转义：

  ```
  'Did she say \'Hello\'?'
  // "Did she say 'Hello'?"
  ```

+ 字符串默认只能写在一行内，分成多行将会报错

+ 字符串可以被视为字符数组，可以使用数组的方括号运算符返回某个位置的字符，但仅此而已，无法修改和增删，只有将字符串显式转为数组后才能使用

+ Base64转码：

  btoa()：字符串或二进制值转为Base64编码

  atob()：Base64编码转为原来的编码

### 对象

+ 对象三种声明方法：

  1. 直接使用大括号{ }生成`var o1 = {};`
  2. 用new命令生成一个Object对象的实例`var o2 = new Object();`
  3. 用Object.create方法生成`Object.create(Object.prototype);`

  对象采用大括号表示导致了一个问题：如果行首是一个大括号，它到底是表达式还是语句？

  为避免歧义，JS规定，如果行首是大括号，一律解释为语句（即代码块）。如果要解释为表达式（即对象），不许在大括号前加上圆括号

  ```
  ({foo:123})
  ```


+ 对象的所有键名都是字符串，加不加引号都可以，非字符串的键名会被转为字符串。但是如果键名不符合标识名的条件，也不是数字，则必须加上引号，否则报错。

+ JS的保留字可以不加引号当作键名

+ 对象的每一个“键名”又称为“属性”，它的“键值“可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为”方法“，它可以像函数那样调用：

  ```
  var o = {
    p: function (x){
      return 2 * x;
    }
  };
  o.p(1) // 2
  ```

+ **不同的变量名指向同一个对象**，他们都是这个对象的引用，也就是说指向同一个内存地址，修改其中的一个变量，会影响到其他所有变量：

  ```
  var o1 = {};
  var o2 = o1;

  o1.a = 1;
  o2.a // 1

  o2.b = 2;
  o1.b // 2
  ```

  如果取消某一个变量对原对象的引用，不会影响到另一个变量：

  ```
  var o1 = {};
  var o2 = o1;

  o1 = 1;
  o2 // {}
  ```

  这种引用只局限于对象，对原始类型的数据则是传值引用，都是值的拷贝

  ```
  var x = 1;
  var y = x;

  x = 2;
  y // 1
  // 当x的值发生变化后，y的值并未改变，表示y和x并不是指向同一个内存地址
  ```

+ **读取对象属性**有两种方法，使用点运算符，使用方括号运算符

  使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理

  数值键名不能使用点运算符，因为会被当成小数点，只能使用方括号运算符，且不用加引号

  方括号运算符内部可以使用表达式

  ```
  o['hello'+'world']
  o[3+3]
  ```

+ 点运算符和方括号运算符，不仅可以用来读取值，还可以用来赋值

  ```
  o.p = 'abc';
  p['p'] = 'abc';
  ```

+ Object.keys方法查看一个对象本身的所有属性：

  ```
  var o = {
    key1: 1,
    key2: 2
  }
  Object.keys(o);
  // ['key1','key2']
  ```

+ delete命令用于删除对象的属性，删除成功后返回true

  ```
  delete o.p
  ```

  delete命令删除一个不存在的属性，不会报错，而且返回true

  delete删除存在且不得删除的属性，返回false

  delete职能删除对象本身的属性，无法删除继承的属性

+ **in运算符**用于检查对象是否包含某个属性（检查的是键名，不是键值），包含返回true，否则返回false：

  ```
  var o = { p:1 };
  'p' in o //true
  ```

  JS中，所有全局变量都是顶层对象（浏览器的顶层对象就是window对象）的属性，因此可以用in运算符判断一个全局变量是否存在

  in运算符不能识别对象继承的属性

+ **for…in循环**用来遍历一个对象所有可便利（enumerable）的属性，会跳过不可遍历的属性

  它不仅遍历对象自身的属性，还遍历继承的属性

### 数组

+ 只要是数组，就一定有length属性。

  length属性是可写的

  如果人为设置一个小于当前成员个数的值，该数组的成员会自动减少到length设置的值，其余的被自动删除

  如果人为设置length大雨当前元素个数，则数组成员数量会增加到这个值，新增的位置都是空位，读取新增的位置会返回undefined

+ 由于数组本质上是对象，所以我们可以为数组添加属性，这不影响length属性的值

+ 类似数组的对象（array-like object）：一个对象的所有键名都是正整数或零，并且有length属性

  “类似数组的对象”并不是数组，它不具备数组特有的方法，比如push方法

  ”类似数组的对象“的根本特征，就是具有length属性，但这种length属性不是动态值，不会随着成员变化而变化

  典型的”类似数组的对象“是函数的arguments对象，大多数DOM元素集，还有字符串

+ **in运算符**同样适用于数组，如果数组某个位置是空位，in运算符返回false

+ **for…in循环**遍历的是对象的所有属性，数组是对象的一种，因此for...in循环也可以遍历数组的所有属性，包括数组的所有数字键和其他属性，因此不推荐用for...in遍历数组

+ **数组的空位**：当数组的某个位置是空元素，即两个逗号之间没有任何值，我们称该数组存在空位。数组的空位不影响length属性：

  ```
  var a = [1, ,1];
  a.length // 3
  ```

  数组的空位可读取，返回undefined

  使用delete命令删除一个数组成员，会形成空位，并且不影响length属性：

  ```
  var a = [1,2,3];
  delete a[1];

  a[1] //undefined
  a.length // 3
  ```

+ 数组的某个位置是空位，和某个位置是undefined是不一样的。

  如果是空位，使用数组的forEach方法、for...in循环、Object.keys方法进行遍历，空位都会被跳过；

  如果是undefined，遍历不会被跳过。

  空位就是数组没有这个元素，所以不会被遍历到，而undefined表示数组有这个元素，值时undefined。

### 函数

+ **函数的三种声明方法**：

  1. function命令声明的代码区块，就是一个函数：

     ```
     function print(s){
       console.log(s);
     }
     ```

  2. 函数表达式：除了用function命令声明，还可以采用变量赋值的写法

     ```
     var print = function(s){
       console.log(s);
     }
     ```

     这种写法将一个匿名函数赋值给变量。这时，这个匿名函数又称函数表达式，因为赋值语句的等号右侧只能放表达式

     采用函数表达式声明函数时，function命令后面不带有函数名。如果加上函数名，该函数名只在函数体内部有效，在函数体外部无效

     ```
     var print = function x(){
       console.log(typeof x);
     };
     x // Error
     print() //function
     ```

     需要注意，函数表达式需要在语句的结尾加上“；”，表示语句结束。

  这两种声明函数的方式，差别很细微，这里可以近似认为是等价的

  3. Function构造函数

     ```
     var add = new Function(
     	'x',
     	'y',
     	'return x + y'
     );
     // 等同于
     function add(x,y){
       return x + y;
     }
     ```

     Function构造函数接受任意数量的参数，只有最后一个参数会被当作函数体，其他参数都是函数的参数；如果只有一个参数，该参数就是函数体

     这种声明函数的方式非常不直观，几乎无人使用

  + 如果同一个函数被多次声明，后面的声明会覆盖前面的声明

    ```
    function f(){
      console.log(1);
    }
    f() // 2
    function f(){
      console.log(2);
    }
    f() // 2
    ```

+ 调用函数时，使用圆括号运算符

  函数体内部的return语句表示返回。JS引擎遇到return语句，就直接返回return后面的那个表达式的值，后面即使还有语句，也不会得到执行。也就是说，return语句所带的那个表达式，就是函数的返回值。return语句不是必需的，如果没有的话，该函数就不返回任何值，或者说返回undefined。

  函数可以调用自身，这就是递归。

+ **第一等公民**：JS语言将函数看作一种值，与其他值（数值、字符串、布尔值等等）地位相同。凡是可以使用值的地方，就能使用函数。比如，可以把函数赋值给变量和对象的属性，也可以当作参数传入其他函数，或者作为函数的结果返回。函数只是一个可以执行的值，此外并无特殊之处。

  由于函数与其他数据类型地位平等，所以在JS中又称函数为第一等公民。

  ```
  function add(x,y){
    return x + y;
  }

  // 将函数赋值给一个变量
  var operator = add;

  // 将函数作为参数和返回值
  function a(op){
    return op;
  }
  a(add)(1,1)
  // 2
  ```

+ **函数名的提升**：JS引擎将函数名视同变量名，所以采用function命令声明函数时，整个函数会像变量声明一样，被提升到代码头部：

  ```
  f();

  function f(){}
  ```

  但是，如果采用赋值语句定义函数，JS就会报错：

  ```
  f();
  var f = function (){};
  // Error!
  ```

  等同于：

  ```
  var f;
  f();
  f = function () {};
  ```

  上面代码第二行，调用f时，f只是被声明了，还没有被赋值，等于undefined，所以会报错。

+ **不能再条件语句中声明函数**：根据ES规范，不得在非函数的代码块中声明函数，最常见的就是if和try语句

+ **函数的属性和方法**：

  + *name属性*，返回紧跟在function关键字之后的那个函数名：

    ```
    function f1(){}
    f1.name // 'f1'

    var f2 = function (){};
    f2.name // ''

    var f3 = function myName(){};
    f3.name // 'myName'
    ```

    匿名函数的name属性总是为空字符串；对于f3来说，返回函数表达式的名字（真正的函数名还是f3，myName这个名字只在函数体内部可用）

  + *length属性*，返回函数预期传入的参数个数，即函数定义之中的参数个数。不管调用时输入了多少个参数，只返回定义时的参数个数。

  + *toString()方法*，返回函数的源码。

+ **函数作用域**

  作用域（scope）指的是变量存在的范围

  JS只有两种作用域：一种是全剧作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在。

  在函数外部声明的变量就是**全局变量**，它可以在函数内部读取。

  在函数内部定义的变量就是**局部变量**，外部无法读取。

  + **函数内部定义的变量，会在该作用域内覆盖同名全局变量**：

  ```
  var v = 1;

  function f(){
    var v = 2;
    console.log(v);
  }

  f() // 2
  v // 1
  ```

  + **函数内部的变量提升**：

    与全局作用域一样，函数作用域内部也会产生“变量提升”现象。var命令声明的变量，不管在什么位置，变量声明都会被提升到函数体的头部。

  + **函数本身的作用域**：

    函数本身也是一个值，也有自己的作用域。它的作用域与变量一样，就是其声明时所在的作用域，与其运行时所在的作用域无关。

    ```
    var a = 1;
    var x = function (){
      console.log(a);
    }

    function f() {
      var a = 2;
      x();
    }

    f() // 1
    ```

    函数执行时所在的作用域，是定义时的作用域，而不是调用时所在的作用域。

    很容易犯错的一点是，如果函数A调用函数B，却没考虑到函数B不会引用函数A的内部变量：

    ```
    var x = function (){
      console.log(a);
    };

    function y(f) {
      var a = 2;
      f();
    }

    y(x)
    // Error: a is not defined
    ```

    同样的，函数体内部声明的函数，作用域绑定函数体内部：

    ```
    function foo(){
      var x = 1;
      function bar(){
        console.log(x);
      }
      return bar;
    }

    var x = 2;
    var f = foo();
    f() // 1
    ```

    这种机制构成了**闭包**。

+ **参数**：ES5中，函数参数不是必需的，JS允许省略参数。

  + ```
    function f(a){
      a = a || 1;
      return a;
    }
    // ||表示“或运算”，赋值语句的等号右边，预期是一个值，凡是预期为值的地方，都可以用表达式
    ```

  + **传递方式**：

    + 函数参数如果是**原始类型**的值（数值、字符串、布尔值），传递方式是**传值传递**。意味着，在函数体内修改参数值，不会影响到函数外部

      ```
      var p = 2;
      function f(p){
        p = 3;
      }
      f(p);

      p // 2
      ```

      变量p是一个原始类型的值，传入函数f的方式是传值传递。因此在函数内部，p的值时原始值的拷贝，无论怎么修改，都不会影响到原始值。

    + 如果函数参数是**复合类型**的值（数组、对象、其他函数），传递方式是**传址传递**。意味着，在函数内部修改参数，将会影响到原始值。

      ```
      var obj = {p: 1};
      function f(o){
        o.p = 2;
      }
      f(obj);

      obj.p // 2
      ```

      上面代码中，传入函数f的是对象obj的地址。因此，在函数内部修改obj的属性p，会影响到原始值。

      **注意**：如果函数内部修改的，不是参数对象的某个属性，而是替换掉整个参数，这时不会影响到原始值：

      ```
      var obj = [1,2,3];
      function f(o){
        0 = [2,3,4];
      }
      f(obj);

      obj // [1,2,3]
      ```

      这是因为，形式参数（o）与实际参数obj存在一个赋值关系

      ```
      // 函数f内部
      o = obj;
      ```

      上面代码中，对o的修改都会反映在obj身上。但是，如果对o赋予一个新的值，就等于切断了o与obj的联系，导致此后的修改都不会影响到obj。

  + 某些情况下，如果需要对某个原始类型的变量，获取传址传递的效果，可以将它写成全局对象的属性：

    ```
    var a = 1;
    function f(p){
      window[p] = 2;
    }
    f('a');

    a // 2
    ```

  + **同名参数**：

    如果有同名的参数，则取最后出现的那个值，即使最后的参数没有值或被省略，也是以其为准：

    ```
    function f(a,a){
      console.log(a);
    }
    f(1,2) // 2
    f(1) // undefined
    ```

  + **arguments对象**：

    arguments对象包含了函数运行时的所有参数，arguments[0是第一个参数，arguments[1]就是第二个，以此类推。这个对象只有在函数体内部可以使用。

    **正常模式下**，arguments对象可以在运行时修改：

    ```
    var f = function (a,b){
      arguments[0] = 3;
      arguments[1] = 2;
      return a + b;
    }
    f(1,1) //5
    ```

    **严格模式下**，arguments对象是一个只读对象，修改它是无效的，但不会报错

    ```
    var f = function (a,b){
      'use strict';
      arguments[0] = 3; // 无效
      arguments[1] = 2; // 无效
      return a + b;
    }
    f(1,1) // 2
    ```

    介绍数组时说过，arguments虽然很像数组，但是它时一个对象，是一个“类似数组的对象”。数组专有的方法，不能在arguments对象上直接使用。

+ **闭包**：

  JS的作用域机制，使得函数外部无法获取函数内部声明的变量。

  但出于某种原因，需要得到函数内部的局部变量。正常情况下是办不到的，只有通过变通方法实现，就是在函数内部，再定义一个函数。

  ```
  function f1(){
    var n = 999;
    function f2(){
      console.log(n); // 999
    }
  }
  ```

  函数f2声明在函数f1的内部，f1内部的所有局部变量，对f2可见。反之则不行。这是JS语言特有的“链式作用域”结构（chain scope），子对象会一级一级的向上寻找所有父对象的变量。

  既然f2可以读取f1的局部变量，那么只要把f2作为返回值，就可以在f1外部读取他的内部变量了

  ```
  function f1(){
    var n = 999;
    function f2(){
      console.log(n);
    }
    return f2;
  }
  var result = f1();
  result(); // 999
  ```

  函数f1点返回值就是函数f2，由于f2可以读取f1的内部变量，所以就可以在外部获得f1点内部变量了。

  **闭包**就是函数f2，即能够读取其他函数内部变量的函数。

  JS语言中，只有函数内部的字函数才能读取内部变量，因此可以把闭包加单理解成**“定义在一个函数内部的函数”**。在本质上，闭包就是将函数内部和函数外部链接起来的一座桥梁。

  **闭包的最大用处有两个**：1. 读取函数内部的变量。2. 让这些变量始终保持在内存中，即闭包可以使得它诞生环境一直存在：

  ```
  function createIncrmenentor(start){
    return function (){
      return start++;
    };
  }
  var inc = createIncrementor(5);

  inc() // 5
  inc() // 6
  inc() // 7
  ```

  *上面代码中，start是函数createIncrementor的内部变量。通过闭包，start的状态被保留，每一次调用都是在上一次调用的基础上进行计算。从中可以看到，闭包inc使得函数createIncrementor的内部环境一直存在。所以，闭包可以看作是函数内部作用域的一个接口。*

  *为什么会这样呢？原因就在于inc始终在内存中，而inc的存在依赖于createIncrementor，因此也是种在内存中，不会再调用结束后，被垃圾回收机制回收。*

  **闭包的另一个用处，是封装对象的私有属性和私有方法。**

  ```
  function Person(name) {
    var _age;
    function setAge(n) {
      _age = n;
    }
    function getAge() {
      return _age;
    }

    return {
      name: name,
      getAge: getAge,
      setAge: setAge
    };
  }

  var p1 = Person('张三');
  p1.setAge(25);
  p1.getAge() // 25
  ```

  **注意**，外层函数每次运行，都会生成一个新的闭包，而这个闭包又会保留外层函数的内部变量，所以内存消耗很大。因此不能滥用闭包，否则会造成网页的性能问题。

+ **立即调用的函数表达式**

  在JS中，一对圆括号（）是一种运算符，跟在函数名之后，表示调用该函数。比如，print（）就表示调用print函数。

  有时，我们需要在定义函数之后，立即调用该函数。这时，闭不能在函数的定义之后加上圆括号，会产生语法错误：

  ```
  function () { /* code */}();
  // SyntaxError: Unexpected token (
  ```

  产生这个错误的原因是，function这个关键字即可以当作语句，也可以当作表达式。同行首是一个对象的大括号，到底是表达式还是语句同理。

  ```
  // 语句
  function f() {}
  // 表达式
  var f = function f() {}
  ```

  为避免解析上的歧义，JS引擎规定，如果function关键字出现在行首，一律解释称语句。因此，JavaScript引擎看到行首是function关键字之后，认为这一段都是函数的定义，不应该以圆括号结尾，所以就报错了。

  解决方法就是不要让function出现在行首，让引擎将其理解成一个表达式。最简单的处理，就是将其放在一个圆括号里面。

  ```
  (function (){ /* code */ }());
  // 或者
  (function (){ /* code */ })();
  ```

  以圆括号开头，引擎就会认为这个一个表达式，而不是函数定义语句，就避免了错误。这就叫做“立即调用的函数表达式”。

  这两种写法最后的分号都是必须的。如果省略分号，遇到连着两个立即调用的函数表达式，可能就会报错。

## 运算符

+ 加号运算符，拼接字符串和加法运算

  ```
  1 + 1 // '2'
  '1' + 1 // '11'
  1 + '1' // '11'

  1+1+1+1 // '4'
  '1'+1+1+1 // '1111'
  1+'1'+1+1 // '1111'
  1+1+'1'+1 // '211'
  1+1+1+'1' // '31'
  ```

  从左往右，能加法运算就加法运算，直到遇到字符，把之前的运算结果和字符拼接，字符之后全部为拼接。

  利用空字符串，将值转为字符串类型

  ```
  x + ''
  ```

+ 余数运算符（%）

  ```
  12 % 5 // 2
  ```

  余数运算的运算结果的正负由第一个运算子的正负决定：

  ```
  -1 % 2 // -1
  1 % -2 // 1
  ```

+ 自增运算符（++）、自减运算符（—)

  自增／自减操作会修改原始变量

  放在变量前，先自增／自减，再返回操作后的值；

  放在变量后，先返回变量的值，再自增／自减

  ```
  var x = 1;
  var y = 1;
  x++ // 1;
  ++y // 2
  ```

+ 数值运算符（+）同样使用加号，但它是一元运算符（只需要一个操作数）。

  数值运算符的作用在于可以将任何值转为数值（与Number函数的作用相同）

  ```
  +true // 1
  +[] // 0
  +{} // NaN
  ```

+ 赋值运算符，除了最常见的等号（=），还有以下：

  ```
  x += y // 等同于 x = x + y
  x -= y // 等同于 x = x - y
  x *= y // 等同于 x = x * y
  x /= y // 等同于 x = x / y
  x %= y // 等同于 x = x % y
  x >>= y // 等同于 x = x >> y
  x <<= y // 等同于 x = x << y
  x >>>= y // 等同于 x = x >>> y
  x &= y // 等同于 x = x & y
  x |= y // 等同于 x = x | y
  x ^= y // 等同于 x = x ^ y
  ```

+ 比较运算符

  如果两个运算子都是字符串，则按照字典顺序比较（实际上是比较Unicode码点）

  否则，将两个运算子都转成数值，再进行比较（等同于先调用Number函数）

+ ==和===的区别

  相等运算符==比较两个值是否相等，严格相等运算符===比较它们是否为“同一个值”。

  如果两个值不是同一类型，严格相等运算符===直接返回 false，而相等运算符==会将它们转化成同一类型，再用严格相等运算符进行比较

+ ===严格相等运算符

  + 同一类型的原始类型的值（数值、字符串、布尔值）比较时，值相同就返回true，不同就返回false。

    ```
    1 === 0x1 // true
    NaN === NaN // false NaN与任何值都不相等，包括自己
    ```

  + 同一类型的复合类型的值（对象、数组、函数）比较时，不是比较它们的值是否相等，而是比较它们是否指向同一个对象

    ```
    {} === {} // false
    [] === [] // false
    (function(){} === function(){}) // false
    ```

    上面代码分别比较两个空对象、两个空数组、两个空函数，结果都是不相等。原因是对于复合类型的值，严格相等运算比较的是，它们是否引用同一个内存地址，而运算符两边的空对象、空数组、空函数的值，都存放在不同的内存地址，结果当然是`false`。

    如果两个变量引用同一个对象，则它们相等。

    ```
    var v1 = {};
    var v2 = v1;
    v1 === v2 // true
    ```

    注意，对于两个对象的比较，严格相等运算符比较的是地址，而大于或小于运算符比较的是值。

+ ==相等运算符

  + 比较相同类型的数据时，与严格相等运算符完全一样

  + 比较不同类型的数据时，相等运算符会先讲数据进行类型转换，然后再用严格相等运算符比较，转换规则如下：
    + 比较原始类型的数据时，会将数据转换成数值类型再进行比较

    + 复合类型（对象、数组、函数）和原始类型的值比较时，复合类型转化成原始类型，再进行比较

      ```
      [1] == 1 // true
      // 等同于 Number([1]) == 1

      [1] == '1' // true
      // 等同于 String([1]) == Number('1')

      [1] == true // true
      // 等同于 Number([1]) == Number(true)
      ```

+ 且运算符&&

  运算规则：如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值：

  ```
  't' && '' // ""
  't' && 'f' // "f"
  't' && (1 + 2) // 3
  '' && 'f' // ""
  '' && '' // ""

  var x = 1;
  (1 - 1) && ( x += 1) // 0
  x // 1
  ```

  if语句中经常这样写：

  ```
  if(x && y){
    /* code */
  }
  ```

  x && y并不直接返回布尔值，而是if命令后面的判断条件预期应该是一个布尔值，JS会自动将其转为布尔值。

+ 或运算符||

  运算规则：如果第一个运算子的布尔值为`true`，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为`false`，则返回第二个运算子的值。

+ void运算符

  void运算符的作用是执行一个表达式，然后不返回任何值，或者说返回undefined。

  ```
  void 0 // undefined
  void(0) // undefined
  ```

  void运算符的主要作用是用于在超链接中插入代码，防止网页跳转。

  ```
  // 点击链接，但不跳转页面
  <a href="javascript:void"></a>

  // 新建窗口打开链接
  <a href="javascript:void window.open('http://example.com')"></a>

  // 提交表单，但不会跳转页面
  <a href="javascript:void(document.form.submit())"></a>
  ```

## 数据类型转换

JS是一种动态类型语言，变量没有类型限制，可以随时赋予任意值。

虽然变量没有类型，但是数据本身和各种运算符是有类型的。如果运算符发现，数据的类型与预期不符，就会自动转换类型。

### 强制转换

强制转换主要指使用Number、String和Boolean三个构造函数，手动将各种类型的值，转换成数字、字符串或布尔值。

+ Number()函数，可以将任意类型的值转化成数值

  1. 原始类型值（字符串，布尔值、undefined、null）

     Number函数将字符串转为数值要比parseInt函数严格很多。只有有一个字符无法转成数值，整个字符串就会被转为NaN。而parseInt是逐个转换字符。

     ```
     // 数值：转换后还是原来的值
     Number(324) // 324

     // 字符串：如果可以被解析为数值，则转换为相应的数值
     Number('324') // 324

     // 字符串：如果不可以被解析为数值，返回NaN
     Number('324abc') // NaN

     // 空字符串转为0
     Number('') // 0

     // 布尔值：true 转成1，false 转成0
     Number(true) // 1
     Number(false) // 0

     // undefined：转成 NaN
     Number(undefined) // NaN

     // null：转成0
     Number(null) // 0
     ```

  2. 复合类型值（对象、数组、函数）

     简单的规则是，`Number`方法的参数是对象时，将返回`NaN`，**除非是包含单个数值的数组**。

     ```
     Number({a: 1}) // NaN
     Number([1, 2, 3]) // NaN
     Number([5]) // 5
     ```

+ String()函数，可以将任意类型的值转化成字符串。转换规则如下：

  1. 原始类型值的转换规则

     ```
     数值：转为相应的字符串。
     字符串：转换后还是原来的值。
     布尔值：true转为"true"，false转为"false"。
     undefined：转为"undefined"。
     null：转为"null"。

     String(123) // "123"
     String('abc') // "abc"
     String(true) // "true"
     String(undefined) // "undefined"
     String(null) // "null"
     ```

  2. 对象的转换规则

     `String`方法的参数如果是对象，返回一个类型字符串；如果是数组，返回该数组的字符串形式。

     ```
     String({a: 1}) // "[object Object]"
     String([1, 2, 3]) // "1,2,3"
     ```

+ Boolean()函数，可以将任意类型的变量转为布尔值，除了以下六个值的转换结果为false，其他值全部是true

  ```
  undefined
  null
  -0
  0或+0
  NaN
  ''（空字符串）
  ```

### 自动转换

以下三种情况，JS会自动转换数据类型，转换时自动完成的，对用户不可见

```
// 1. 不同类型的数据互相运算
123 + 'abc' // "123abc"

// 2. 对非布尔值类型的数据求布尔值
if ('abc') {
  console.log('hello')
}  // "hello"

// 3. 对非数值类型的数据使用一元运算符（即“+”和“-”）
+ {foo: 'bar'} // NaN
- [1, 2, 3] // NaN
```

自动转换的规则是这样的：预期什么类型的值，就调用该类型的转换函数。比如，某个位置预期为字符串，就调用`String`函数进行转换。如果该位置即可以是字符串，也可能是数值，那么默认转为数值。

## 错误处理机制

### Error对象

JavaScript解析或执行时，一旦发生错误，引擎就会抛出一个错误对象。JavaScript原生提供一个`Error`构造函数，所有抛出的错误都是这个构造函数的实例。

```
var err = new Error('出错了');
err.message // "出错了"
```

Error构造函数接受一个参数，表示错误提示，可以从实例等message属性读到这个参数。

Error对象的实例必须有message属性。大多数JS引擎，对Error实例还提供name和stack属性，分别表示错误的名称和错误的堆栈，但她们是非标准的，不是每种实现都有

### JS的原声错误类型

Error对象时最易班的错误类型。在它的基础上，JS还定义了其他6种错误：

1. SyntaxError是解析代码时发生的语法错误
2. ReferenceError是引用一个不存在的变量时发生的错误或将一个值分配给无法分配的对象
3. RangeError
4. TypeError
5. URIError
6. EvalError

### 自定义错误

除了JavaScript内建的7种错误对象，还可以定义自己的错误对象。

```
function UserError(message) {
   this.message = message || "默认信息";
   this.name = "UserError";
}

UserError.prototype = new Error();
UserError.prototype.constructor = UserError;
```

上面代码自定义一个错误对象UserError，让它继承Error对象。然后，就可以生成这种自定义的错误了。

```
new UserError("这是自定义的错误！");
```

### throw语句

throw语句的作用是中断程序执行，抛出一个意外或错误。它接受一个表达式作为参数们可以抛出各种值。

JS引擎一旦遇到throw语句，就会停止执行后面的语句，并将throw语句的参数值，返回给用户。

```
throw new Error('出错了！');
```

### try…catch结构

try…catch结构对错误进行处理

```
tru{
  throw new Error('出错了！');
}catch(e){
  console.log(e.name + ":" + e.message);
  console.log(e.stack)
}
```

try代码块一抛出错误（上例用的是throw语句），js引擎就立即把代码的执行，转到catch代码块。catch接受一个参数，表示try代码块抛出的值。

catch代码块中，还可以再抛出错误，甚至使用嵌套的try...catch结构。

# 面向对象编程

面向对象编程（Object Oriented Programming，OOP）是目前主流的编程范式。

对象是一个容器，封装了属性（property）和方法（method）。

## 构造函数

典型的面向对象编程语言（C++，Java），存在“类”（class）的概念。所谓“类”就是对象的模版，对象就是“类”的实例。JS语言的对象体系，不是基于“类”的，而是基于构造函数（constructor）和原型链（prototype）。

JS语言使用构造函数作为对象的模版。所谓“构造函数”，就是专门用来生成对象的函数。它提供模版，描述对象的基本结构。一个构造函数，可以生成多个对象，这些对象都有相同的结构。

构造函数的写法就是一个普通的函数，但是有自己的特征和用法。为了和普通函数区别，构造函数名字的第一个字母通常大写：

```
var Vehicle = function () {
  this.price = 1000;
};
```

1. 函数体内部使用了this关键字，代表了所要生成的对象实例。
2. 生成对象的时候，必需用new命令。

## new命令

new命令的作用是，执行构造函数，返回一个对象实例。

```
var Vehicle = function () {
  this.price = 1000;
};
var v = new Vehicle();
v.price // 1000
```

new命令执行时，构造函数内部的this，就代表了新生成的实例对象。

使用new命令时，根据需要，构造函数也可以接受参数：

```
var Vehicle = function (p) {
  this.price = p;
};
var v = new Vehicle(500);
```

new命令本身就可以执行构造函数，所以后面的构造函数可以带括号，也可以不带括号：

```
var v = new Vehicle();
var v = new Vehicle;
```

### new命令的原理

使用new命令时，她后面的函数调用就不是正常的调用，而是依次执行下面的步骤：

1. 创建一个空对象，作为将要返回的对象实例
2. 将这个空对象的原型，指向构造函数的prototype属性
3. 将这个空对象赋值给函数内部的this关键字
4. 开始执行构造函数内部的代码

构造函数内部，this指的是一个新生成的空对象，所有针对this的操作，都会发生在这个空对象上。构造函数之所以叫“构造函数”，就是说这个函数的目的，就是操作一个空对象（即this对象），将其“构造“为需要的样子。

如果构造函数内部有return语句，而且return后面跟一个对象，new命令会返回return语句指定的对象；否则，就会不管return语句，返回this对象。

```
var Vehicle = function () {
  this.price = 1000;
  return 1000;
};

(new Vehicle()) === 1000
// false

var Vehicle = function (){
  this.price = 1000;
  return { price: 2000 };
};

(new Vehicle()).price
// 2000
```

如果对普通函数（内部没有this关键字的函数）使用new命令，则会返回一个空对象。

### 直接调用构造函数会发生什么？

如果忘了使用new命令，直接调用构造函数会发生什么？

这种情况下，构造函数就编程了普通函数，并不会生成实例对象。而且this这时代表全局对象，将造成一些意想不到的后果。

```
var Vehicle = function () {
  this.price = 1000;
}

var v = Vehicle();
v.price
// Uncaught TypeError: Cannot read property 'price' of undefined

price
// 1000
```

不使用new命令调用Vehicle构造函数时，price属性变成了全局变量，变量v变成了undefined。

### new.target

函数内部可以使用new.target属性。如当前函数是new命令调用，new.target指向当前函数，否则为undefined。

## 使用Object.create()创建实例对象

构造函数作为模版，可以生成实例对象。但是，有时只能拿到实例对象，而该对象根本就不是由构造函数生成的。这时可以使用Object.create()方法，直接以某个实例对象作为模版，生成一个新的实例对象。

```
var person1 = {
  name: '张三',
  age: 38,
  greeting: function() {
    console.log('Hi! I\'m ' + this.name + '.');
  }
};

var person2 = Object.create(person1);

person2.name // 张三
person2.greeting() // Hi! I'm 张三.
```

上面代码中，对象person1是person2的模版，后者继承了前者的属性和方法。

## this关键字

this总是返回一个对象，简单说就是返回属性或方法“当前”所在的对象:

```
var person = {
  name : 'Ethan',
  describe: function () {
    return '姓名:'+this.name;
  }
};
person.describe()
// “姓名：Ethan”
```

this.name表示describe方法所在的当前对象的name属性。调用person.describe方法时，describe方法所在的当前对象是person，所以就是调用person.name。

**由于对象的属性可以赋给另一个对象，所以属性所在的当前对象是可变的，即this的指向是可变的。**

```
function f() {
  return '姓名：'+ this.name;
}

var A = {
  name: '张三',
  describe: f
};

var B = {
  name: '李四',
  describe: f
};

A.describe() // "姓名：张三"
B.describe() // "姓名：李四"
```

只要函数被赋给另一个变量，this的指向就会变：

```
var A = {
  name: '张三',
  describe: function () {
    return '姓名：'+ this.name;
  }
};

var name = '李四';
var f = A.describe;
f() // "姓名：李四"
```

A.describe被赋值给变量f，内部的this就会指向f运行时所在的对象（本例是顶层对象）。

```
<input type="text" name="age" size=3 onChange="validate(this, 18, 99);">

<script>
function validate(obj, lowval, hival){
  if ((obj.value < lowval) || (obj.value > hival))
    console.log('Invalid Value!');
}
</script>
```

上面代码是一个文本输入框，每当用户输入一个值，就会调用onChange回调函数。回调函数传入this，就代表传入当前对象（即文本框），然后就可以从this.value上面独到用户的输入值。

JS语言中，一切皆对象，运行环境也是对象，所以函数都是在某个对象之中运行，this就是这个对象（环境）。

如果一个函数在全局环境中运行，那么this就是指顶层对象（浏览器中为window对象）。

```
function f(){
  return this;
}
f() === window // true
```

可以近似地认为，this是所有函数运行时的一个隐藏参数，指向函数的运行环境。 

### 使用场合

this的使用可以分成以下几个场合。

1. **全局环境**使用this，它指的就是顶层对象window。

   ```
   this === window // true

   function f(){
     soncole.log(this === window); // true
   }
   ```

   不管是不是在函数内部，只要是在全局环境下运行，this就是指顶层对象window。

2. **构造函数**中的this，指的是实例对象。

3. **对象的方法**

   当A对象的方法被赋予B对象，该方法中的this就是从指向A对象变成了指向B对象。所以要特别小心，将某个对象的方法赋值给另一个对象，会改变this的指向。

   ```
   var obj = {
     foo: function(){
       console.log(this);
     }
   };
   obj.foo() // obj
   ```

   obj.foo方法执行时，它内部的this指向obj。

   但是，只有这一种用法（直接在obj对象上调用foo方法），this指向obj；其他用法时，this都指向代码块当前所在对象（浏览器为window对象）。

   ```
   // 情况一
   (obj.foo = obj.foo)() // window

   // 情况二
   (false || obj.foo)() // window

   // 情况三
   (1, obj.foo)() // window
   ```

   上面代码中，obj.foo先运算在执行，即使值根本没有变化，this也不再指向obj了。这时因为这时它就脱离了运行环境obj，而是在全局环境执行。

   可以这样理解，在JS引擎内部，obj和obj.foo储存在两个内存地址，假设时M1和M2。只有obj.foo（）这样调用时，是从M1调用M2，因此this指向obj。但是，上面三种情况，都是直接取出M2进行运算，然后在全局环境执行运算结果（还是M2），因此this指向全局环境。

   **如果某个方法位于多层对象的内部，这时this只是指向当前一层的对象，而不会继承更上面的层**：

   ```
   var a = {
     p: 'Hello',
     b: {
       m: function(){
         console.log(this.p);
       }
     }
   };

   a.b.m() // undefined
   ```

   上面代码中，a.b.m方法在a对象的第二层，该方法内部的this不是指向a，而是直系那个a.b。

### 使用注意

1. 避免多层this

   ```
   var o = {
     f1:function(){
       console.log(this);
       var f2 = function (){
         console.log(this);
       }();
     }
   }

   o.f1()
   // Object
   // Window
   ```

   第一层指向该对象，第二层指向全局对象

   [**为什么第二层this指向全局对象window？**](http://blog.csdn.net/u011457225/article/details/73882551?utm_source=itdadao&utm_medium=referral)

   o.f1()表示调用o对象的f1方法，所以f1方法中的this指向o对象；而f1方法中的闭包（即f2函数）不属于任何对象，它不是一个对象的方法。非严格模式下，如果没有指定属于哪个对象，那么它就属于window对象。

### 绑定this的方法

JS提供了call、apply、bind三种方法，来切换／固定this 的指向。

1. **function.prototype.call()**

   作用：指定函数内部this的指向（即函数执行时所在的作用域），然后在所指定的作用域中，调用该函数。

   call方法的参数，应该是一个对象。如果参数为空、null和undefined，则默认传入全局对象。如果参数是一个原始值，那么这个原始值回自动转成对应的包装对象，然后传入call方法。

   ```
   var n = 123;
   var obj = { n: 456};

   function a(){
     console.log(this.n);
   }

   a.call() // 123
   a.call(null) // 123
   a.call(undefined) // 123
   a.call(window) // 123
   a.call(obj) // 456
   ```

   call方法还可以接受多个参数，第一个参数就是this所要指向的那个对象，后面的参数则是函数调用时所需的参数。

   ```
   func.call(thisValue,arg1,arg2,...)
   ```

   ```
   function add(a,b){
     return a + b;
   }

   add.call(this,1,2) // 3
   ```

2. **function.prototype.apply()**

   apply方法等作用域call方法类似，也是改变this指向，然后在调用该函数。唯一的区别就是，他接受一个数组作为函数执行时的参数：

   ```
   func.apply(thisValue,[arg1,arg2,...])
   ```

   ```
   function f(x,y){
     console.log(x+y);
   }
   f.call(null,1,1) // 2
   f.apply(null,[1,1]) // 2
   ```

3. **function.prototype.bind()**

   apply／call方法不仅绑定函数执行时所在的对象，还会立即执行函数。

   bind方法只用于将函数体内的this绑定到某个对象，然后返回一个新函数。

## prototype 对象

### 原型对象

通过同一个构造函数创建的实例对象之间，无法共享属性，对系统资源造成浪费。

**JS的每个对象都继承另一个对象，后者称为“原型”（prototype）对象。null除外。**

原型对象上的所有属性和方法，都能派生对象共享。这就是JS继承机制的基本设计。

通过构造函数生成实例对象时，会自动为实例对象分配原型对象。**每一个构造函数都有一个prototype属性**，这个属性就是实例对象的原型对象。

```
function Animal (name){
  this.name = name;
}

Animal.prototype.color = 'white';

var cat1 = new Animal('大毛');
var cat2 = new Animal('二毛');

cat1.color // 'white'
cat2.color // 'white'
```

构造函数Animal的prototype对象，就是实例对象cat1和cat2的原型对象。在原型对象上添加一个color属性。实例对象都能读取该属性。

+ 原型对象的属性不是实例对象自身的属性。只要修改原型对象，变动就立刻会体现在所有实例对象上。


+ 当实例对象本身没有某个属性或方法等时候，它会去原型对象寻找并读取这个属性火方法。
+ 如果实例对象自身就有某个属性或方法，它就不会再去原型对象寻找这个属性或方法。

**原型对象的作用，就是定义所有实例对象共享的属性和方法。**

### 原型链

原型对象本身也是对象，又有自己的原型，所以形成一条原型链。比如，a对象是b对象的原型，b对象是c对象的原型，以此类推。

如果一层层地上溯，所有对象的原型最终都可以上溯到`Object.prototype`，即Object构造函数的prototype属性指向的那个对象。`Object.prototype`对象的原型是没有任何属性和方法等`null`对象，而`null`对象没有自己的原型。

+ 原型链的作用是：读取对象的某个属性时，JS引擎线寻找对象本身的属性，如果找不到，就到它的原型去找，如果还找不到，就到原型的原型去找。如果知道最顶层的`Object.prototype`还是找不到，则返回undefined。

在原型链一级级向上寻找某个属性，对性能有影响。属性越在山层影响越大。如果寻找某个不存在的属性，会遍历整个原型链。

```
var MyArray = function (){};

MyArray.prototype = new Array();
MyArray.prototype.constructor = MyArray;

var mine = new MyArray();
mine.push(1,2,3);

mine.length // 3
mine instanceof Array // true
```

instanceof运算符用来比较一个对象是否为某个构造函数的实例。

### constructor属性

prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数。

```
function P (){}

P.prototype.constructor === p // true
```

由于constructor属性定义在prototype对象上面，意味着可以被所有实例对象继承：

```
function P () {}
var p = new P();

p.constructor
// function P() {}

p.constructor === P.prototype.constructor
// true

p.hasOwnProperty('constructor')
//false
```

+ **constructor属性的作用，是分辨原型对象到底属于哪个构造函数**

+ 通过constructor属性，从实例新建另一个实例：

  ```
  function Constr() {}
  var x = new Constr();

  var y = new x.constructor();
  y instanceof Constr // true
  ```

  这使得在实例方法中，调用自身的构造函数成为可能：

  ```
  Constr.prototype.createCopy = function (){
    return new this.constructor();
  }
  ```

+ 通过name属性，可以从实例得到构造函数的名称：

  ```
  function Foo () {}
  var f = new Foo();
  f.constructor.name // "Foo"
  ```

+ 修改原型对象时，一般要同时校正constructor属性的指向，否则会导致instanceof运算符失真。

### instanceof运算符

instanceof运算符返回一个布尔值，表示指定对象是否为某个构造函数的实例。

```
var n = new Vehicle();
v instanceof Vehicle // true
```

instanceof运算符左边时实例对象，右边是构造函数。它会检查右边构造函数的原型对象，是否在左边对象的原型链上。

instanceof运算符只能用于对象，不适用原始类型的值。

### Object.getPrototypeOf()

`Object.getPrototypeOf`方法返回一个对象的原型。这时获取原型对象的标准方法。

```
// 空对象的原型是Object.prototype
Object.getPrototypeOf({}) === Object.prototype
// true

// 函数的原型是Function.prototype
function f() {}
Object.getPrototypeOf(f) === Function.prototype
// true

// f 为 F 的实例对象，则 f 的原型是 F.prototype
var f = new F();
Object.getPrototypeOf(f) === F.prototype
// true
```

### Object.setPrototypeOf()

`Object.setPrototypeOf`方法可以为现有对象设置原型，返回一个新对象。

`Object.setPrototypeOf`方法接受两个参数，第一个是现有对象，第二个是原型对象。

`new`命令通过构造函数新建实例对象，实质就是将实例对象的原型，指向构造函数的`prototype`属性，然后在实例对象上执行构造函数。

```
var F = function () {
  this.foo = 'bar';
};

var f = new F();

// 等同于
var f = Object.setPrototypeOf({}, F.prototype);
F.call(f);
f.foo // "bar"
```

### Object.create()

`Object.create`方法是为了满足只能通过实例对象生成另一个实例对象的需求。

`Object.create`方法接受一个对象作为**第一个参数**，然后以它为原型，返回一个实例对象。该实例完全继承原型对象的属性。

老式浏览器如果不支持Object.create方法，可以用下面代码部署：

```
if (typeof Object.create !== 'function'){
  Object.create = function (obj){
    function F(){}
    F.prototype = obj;
    return new F()
  };
}
```

+ Object.create方法参数不能为空，或者不是对象，否则会报错。

+ 如果想生成一个不继承任何属性的对象，可以将Object.create的参数设为null。

+ Object.create方法生成的新对象，动态继承原型。在原型上添加或修改任何方法，会立刻反应在新对象之上。

+ Object.create方法还接受**第二个参数**。该参数是一个属性描述对象，它所描述的对象属性，会添加到实例对象，作为该对象自身的属性

  ```
  var obj = Object.create({}, {
    p1: {
      value: 123,
      enumerable: true,
      configurable: true,
      writable: true,
    },
    p2: {
      value: 'abc',
      enumerable: true,
      configurable: true,
      writable: true,
    }
  });

  // 等同于
  var obj = Object.create({});
  obj.p1 = 123;
  obj.p2 = 'abc';
  ```

+ Object.create方法生成的对象，继承了它原型对象的构造函数。

### Object.prototype.isPrototypeOf()

对象实例的isPrototypeOf方法，用来判断一个对象是否是另一个对象的原型。

```
var o1 = {};
var o2 = Object.create(o1);
var o3 = Object.create(o2);

o2.isPrototypeOf(o3); // true
o1.isPrototypeOf(o3); // true
```

**只要某个对象处在原型链上，isPrototypeOf都返回true。**

### Object.prototype.\_\_proto\_\_

\_\_proto\_\_属性（前后各两个下划线）可以改写某个对象的原型对象。

```
var obj = {};
var p = {};

obj.__proto__ = p;
Object.getPrototypeOf(obj) === p // true
```

### 获取原型对象方法等比较

\_\_proto\_\_属性指向当前对象的原型对象，即构造函数的prototype属性：

```
var obj = new Object();

obj.__proto__ === Object.prototype
//true
obj.__proto__ === obj.constructor.prototype
//true
```

因此，获取实例对象obj的原型对象，有三种方法：

+ obj.\_\_proto\_\_
+ obj.constructor.prototype
+ Object.getPrototypeOf(obj)

前两种方法都不可靠，推荐第三种。

## Object对象与继承

通过原型链，对象的属性分成两种：自身的属性和继承的属性。

### Object.getOwnPropertyNames()

`Object.getOwnPropertyNames`方法返回一个数组，成员是对象本身的所有属性的键名，不包含继承的属性键名。

对象本身的属性之中，有的是可以枚举的，有的不可枚举。`Object.getOwnPropertyNames`方法返回包括不可枚举的所有键名。如果只获取可枚举属性，请使用`Object.keys`方法。

### Obejct.prototype.hasOnwProperty()

对象实例的`hasOwnProperty`方法返回一个布尔值，用于判断某个属性定义在对象自身，还是定义在原型链上：

```
Date.hasOwnProperty('length')
// true

Date.hasOwnProperty('toString')
// false
```

hasOwnProperty方法是JS中唯一一个处理对象属性时，不会遍历原型链的方法。

### in运算符和for...in循环

+ in运算符返回一个布尔值，表示一个对象是否具有某个属性。它不区分该属性是对象自身的属性，还是继承的属性。
+ 获得对象的所有可枚举属性（不管是自身的还是继承的），可以使用for...in循环。

### 对象的拷贝

如果要拷贝一个对象，需要做到下面两件事情。

+ 确保拷贝后的对象，与原对象具有同样的prototype原型对象。
+ 确保拷贝后的对象，与原对象具有同样的属性。

# 标准库

## Object对象

JS原生提供Object对象（起首的O大写），所有其他对象都继承自这个对象。

Object本身也是一个构造函数，可以直接通过它来生成新对象。

Object作为构造函数使用时，可以接受一个参数。如果该参数是一个对象，则直接返回这个对象；如果是一个原始类型的值，则返回该值对应的包装对象。

## Array对象

Array是JS的内置对象，同时也是一个构造函数，可以用它生成新的数组。

## 包装对象

所谓“包装对象”，就是分别与数值、字符串、布尔值相对应的Number、String、Boolean三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。

```
var v1 = new Number(123);
var v2 = new String('abc');
var v3 = new Boolean(true);
```

Number、String和Boolean如果不作为构造函数调用（即调用时不加new），常常用于将任意类型的值转为数值、字符串和布尔值。

```
Number(123) // 123
String('abc') // "abc"
Boolean(true) // true
```

总之，这三个对象作为构造函数使用（带有new）时，可以将原始类型的值转为对象；作为普通函数使用时（不带有new），可以将任意类型的值，转为原始类型的值。

## Math对象

Math是JS的内置对象，提供一系列数学常数和数学方法。该对象不是构造函数，不能生成实例，所有的属性和方法都必须在Math对象上调用。

## Date对象

Date对象可以作为普通函数直接调用，返回一个代表当前时间的字符串。

Date对象也可以作为构造函数使用。使用new命令，会返回一个Date对象的实例。如果不加参数，生成的就是代表当前时间的对象。

## 属性描述对象

JS提供了一个内部数据结构，用来描述一个对象的属性的行为，控制它的行为。这被称为“属性描述对象“（attributes object）。每个属性都有自己对应的属性描述对象，保存该属性的一些元信息。

下面是属性描述对象的一个实例。

```
{
  value:123,
  writable:false,
  enumerable:true,
  configurable:false,
  get:undefined,
  set:undefined
}
```

属性描述对象提供6个元属性：

1. value

   value存放该属性的属性值，默认为undefined。

2. writable

   writable存放一个布尔值，表示属性值（value）是否可改变，默认为true。

3. enumerable

   enumerable存放一个布尔值，表示该属性是否可枚举，默认为true。如果设为false，会使得某些操作（比如for...in循环、Object.keys()）跳过该属性。

4. configurable

   configurable存放一个布尔值，表示“可配置性”，默认为true。如果设为false，将阻止某些操作改写该属性，比如，无法删除该属性，也不得改变该属性的属性描述对象（value属性除外）。也就是说，configurable属性控制了属性描述对象的可写性。

5. get

   get存放一个函数，表示该属性的取值函数（getter），默认undefined。

6. set

   set存放一个函数，表示该属性的存值函数（setter），默认为undefined。

### Object.getOwnPropertyDescriptor()

`Object.getOwnPropertyDescriptor`方法可以读出对象自身属性的属性描述对象。

```
var o = { p : 'a' };

Object.getOwnPropertyDescriptor(o,'p')
// Object { value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

### Object.defineProperty(),Object.defineProperties()

`Object.defineProperty()`方法允许通过定义属性描述对象，来定义或修改一个属性，然后返回修改后的对象。它的格式如下：

```
Object.defineProperty(object,propertyName,attributesObject)
```

`Object.defineProperty`方法接受三个参数，第一个是属性所在的对象，第二个是属性名（它应该是一个字符串），第三个是属性的描述对象。比如新建一个o对象，并定义它的p属性，写法如下：

```
var o = Object.defineProperty({},'p',{
  value:123,
  writable:false,
  enumerable:true,
  configurable:false
})
o.p
// 123
o.p = 246;
o.p
// 123
// 因为writable为false，所以无法改变该属性的值。
```

如果属性已经存在，`Object.defineProperty`方法相当于更新该属性的属性描述对象。

需要注意的是，Object.defineProperty方法和后面的Object.defineProperties方法，都有性能损耗，会拖慢执行速度，不宜大量使用。

如果一次性定义或修改多个属性，可以使用`Object.defineProperties`方法：

```
var o = Object.defineProperties({},{
  p1: { value: 123, enumerable: true },
  p2: { value: 'abc', enumerable: true },
  p3: { get: function(){ return this.p1 + this.p2 },
  	enumerable:true,
  	configurable:true
  }
});

o.p1 // 123
o.p2 // "abc"
o.p3 // "123abc"
```

上面代码的p3属性，定义了取值函数get。**这时需要注意的是，一旦定义了取值函数get（或存值函数set），就不能将writable设为true，或者同时定义value属性，会报错。**

```
var o = {};

Object.defineProperty(o,'p',{
  value:123,
  get: function (){ return 456; }
});
// TypeError: Invalid property.
// A property cannot both have accessors and be writable or have a value,
```

### 元属性

#### 可枚举性（enumerable）

只有可枚举的属性，才会被for...in循环遍历，同时还规定原生继承的属性都是不可枚举的。

可枚举性涌来控制所描述的属性，是否将被包括在for...in循环之中。具体来说，如果一个属性的enumerable为false，下面三个操作不会渠道该属性。

+ for…in循环
+ Object.keys方法
+ JSON.stringify方法

基本上，JS原生提供的属性都是不可枚举的，用户自定义的属性都是可枚举的。

#### 可配置性（configurable）

可配置性决定了是否可以修改属性描述对象。也就是说，当configurable为false的时候，value、writable、enumerable和configurable都不能被修改了。

需要注意的是，writable只有在从false改为true会报错，从true改为false则是允许的。

```
var o = Object.defineProperty({},'p',{
  writable: true,
  configurable: false
});

Object.defineProperty(o,'p',{writable: false})
// 修改成功
```

至于value，只要writable和configurable有一个为true，就允许改动。

```
var o1 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: true,
  configurable: false
});

Object.defineProperty(o1,'p', {value: 2})
// 修改成功

var o2 = Object.defineProperty({}, 'p', {
  value: 1,
  writable: false,
  configurable: true
});

Object.defineProperty(o2,'p', {value: 2})
// 修改成功
```

configurable为false时，直接对该属性赋值，不报错，但不会成功。

```
var o = Object.defineProperty({},'p',{
  value: 1,
  configurable: false
});

o.p = 2;
o.p // 1
```

上面代码中，o对象的p属性是不可配置的，对他赋值是不会生效的。

可配置性决定了一个变量是否可以被删除。

```
var o = Object.definePropertyies({},{
  p1: { value: 1, configurable: true },
  p2: { value: 2, configurable: false}
});

delete o.p1 // true
delete o.p2 // false

o.p1 // undefined
o.p2 // 2
```

#### 可写性（writable）

可写性决定了属性的值是否可以被改变。

```
var o = {};
Object.defineProperty(o,'a',{
  value: 37,
  writable:false
});

o.a // 37
o.a = 25;
o.a // 37
```

正常模式下，对可写性为false的属性赋值不会报错，只会默默失败。但是严格模式下会报错。

关于可写性，还有一种特殊情况。就是如果原型对象的某个属性的可写性为false，那么派生对象将无法自定义这个属性。

```
var proto = Object.defineProperty({},'foo',{
  value: 'a',
  writable: false
});

var o = Object.create(proto);

o.foo = 'b';
o.foo // 'a'
```

规避这个情况的方法，就是通过覆盖属性描述对象，绕过这个限制，原因是这种情况下，原型链会被完全忽视。

```
Object.defineProperty(o,'foo',{
  value:'b'
});
o.foo // 'b'
```

### 存取器（accessor）

除了直接定义以外，属性还可以用存取器（accessor）定义。其中，存值函数称为setter，使用set命令；取值函数称为getter，使用get命令。

存取器提供的是虚拟属性，即该属性的值不失时机存在的，而是每次读取时计算生成的。

```
var o = {
  get p(){
    return 'getter';
  },
  set p(value){
    console.log('setter:' + value);
  }
};

o.p // "getter"
o.p = 123 // "setter: 123"
```

上面代码中，o对象内部的get和set命令，分别定义了p属性的取值函数和存值函数。定义了这两个函数之后，对p属性取值时，取值函数会自动调用；对p属性赋值时，存值函数会自动调用。

取值函数getter不能接受参数，存值函数setter只能接受一个参数（即属性的值）。

对象也不能有和与之函数同名的属性。

存取器也可以通过Object.defineProperty定义：

```
var d = new Date();
Object.definedProperty(d,'month',{
  get: function (){
    return d.getMonth();
  },
  set: function (v){
    d.setMonth(v);
  }
});
```

**利用存取器，可以实现数据对象与DOM对象的双向绑定。**

```
Object.defineProperty(user,'name',{
  get: function(){
    return document.getElementById('foo').value
  },
  set: function(newValue){
    document.getElementById('foo').value = newValue
  },
  configurable: true
})
```

上面代码，将DOM对象foo与数据对象user的name属性，实现了绑定。两者之中只要有一个对象发生变化，就能在另一个对象上实时反映出来。

# 语法专题

## 单线程模型

### 含义

单线程模型指的是，JS值在一个线程上运行。也就是说，JS同时只能执行一个任务，其他任务都必须在后面排队等待。

JS只在一个线程上运行，不代表JS引擎只有一个线程。JS有多个线程。

### 消息队列

JS运行时，除了一个运行线程，JS还提供一个消息队列，里面是各种需要当前程序处理的消息。新的消息进入队列时，会自动排在队列尾端。

运行线程只要发现消息队列不为空，就会取出排在第一位的消息，执行它对应的回调函数。等执行完，再取出排在第二位的消息，不断循环，直到消息队列变空为止。

进入消息队列的消息，必须有对应的回调函数。否则这个消息就会遗失。

setTimeout会在指定时间向消息队列添加一条消息。如果消息队列之中，此时没有其他消息，这条消息会立即得到处理；否则，这条消息会不得不等到其他消息处理完，才会得到处理。因此，`setTimeout`指定的执行时间，只是一个最早可能发生的时间，并不能保证一定会在那个时间发生。

### Event Loop

用来一轮又一轮的处理消息队列之中的消息，即执行对应的回调函数的内部循环机制。

常见的JS任务：

+ 执行JS代码
+ 对用户的输入（鼠标点击、键盘输入等等）做出反应
+ 处理异步网络请求

所有任务可以分成两种，一种是同步任务，另一种是异步任务。

同步任务指的是，在JavaScript执行进程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入JavaScript执行进程、而进入“任务队列”（task queue）的任务，只有“任务队列”通知主进程，某个异步任务可以执行了，该任务（采用回调函数的形式）才会进入JavaScript进程执行。

以Ajax操作为例，它可以当作同步任务处理，也可以当作异步任务处理，由开发者决定。如果是同步任务，主线程就等着Ajax操作返回结果，再往下执行；如果是异步任务，该任务直接进入“任务队列”，JavaScript进程跳过Ajax操作，直接往下执行，等到Ajax操作有了结果，JavaScript进程再执行对应的回调函数。

## 定时器

JS提供定时执行代码的功能，叫做定时器（timer），主要由`setTimeout()`和`setInterval()`这两个函数来完成。它们向任务队列添加定时任务。

### setTimeout()

用来指定某个函数或某段代码，在多少毫秒之后执行。

setTimeout函数接受两个参数，第一个参数`fund|code`是将要推迟执行的函数名或者一段代码，第二个参数`delay`是推迟执行的毫秒数。

```
console.log(1);
setTimeout('console.log(2)',1000);
console.log(3);
```

**注意**，推迟执行的代码必须以字符串的形式放入setTimeout，因为引擎内部使用eval函数，将字符串转为代码。如果推迟执行的是函数，则可以直接将函数名放入setTimeout。

一方面eval函数有安全顾虑，另一方面为了便于引擎优化代码，setTimeout方法一般总是采用匿名函数的形式：

```
function f(){
  console.log(2);
}

setTimeout(f,1000);

// 或者

setTimeout(function () {
  console.log(2)
},1000)
```

### setInterval()

setInterval函数的用法与setTimeout完全一致，区别仅仅在于setInterval指定某个任务每隔一段时间就执行一次，也就是无限次的定时执行。

### clearTimeout()，clearInterval()

setTimeout和setInterval函数，都返回一个表示计数器编号的整数值，将该整数值传入clearTimeout和clearInterval函数，就可以取消对应的定时器。

```
var id1 = setTimeout(f,1000);
var id2 = setInterval(f,1000);

clearTimeout(id1);
clearInterval(id2);
```

### 运行机制

`setTimeout`和`setInterval`的运行机制是，将指定的代码移出本次执行，等到下一轮 Event Loop 时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮 Event Loop 时重新判断。

## Promise对象

### JS的异步执行

#### 回调函数

回调函数是异步编程最基本的方法。

假定有两个函数f1和f2，后者必须等到前者执行完成才能执行。这时可以考虑改写f1，把f2写成f1的回调函数。

```
function f1(callback){
  // f1 的代码
  
  // f1 执行完成后，调用回调函数
  callback();
}

f1(f2);
```

回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪（尤其是回调函数嵌套的情况），而且每个人物只能指定一个回调函数。

#### Promise对象

Promise对象是CommonJS工作组提出的一种规范，目的是为异步操作提供统一接口。

它是一个对象。它使得异步操作具备同步操作的接口，使得程序具备正常的同步运行的流程，回调函数不必再一层层嵌套。

它的思想是，每一个异步任务立刻返回一个Promise对象，由于是立刻返回，所以可以蚕蛹同步操作的流程。这个Promise对象有一个then方法，允许指定回调函数，在异步任务完成后调用。

比如，异步操作f1返回一个Promise对象，它的回调函数f2写法如下：

```
(new Promise(f1)).then(f2);
```

#### Promise接口

Promise接口的基本思想是，异步任务返回一个Promise对象。

Promise对象只三种状态：

+ 异步操作“未完成”（pending）
+ 异步操作“已完成”（resolved）
+ 异步操作“失败”（rejected）

三种状态只有两种变化方式：

+ 异步操作从“未完成”到“已完成”
+ 异步操作从“未完成”到“失败”

因此，Promise对象的最终结果只有两种：

+ 异步操作成功，Promise对象传回一个值，状态变为resolved。
+ 异步操作失败，Promise对象抛出一个错误，状态变为rejected。

Promise对象使用then方法添加回调函数。then方法可以接受两个回调函数，第一个是异步操作成功时的回调函数，第二个时异步操作失败时的回调函数（可以省略）。一旦状态改变，就调用相应的回调函数。

```
// po是一个Promise对象
po.then(
	console.log,
	console.error
)
```

then方法可以链式使用：

```
po
	.then(step1)
	.then(step2)
	.then(step3)
	.then(
		console.log,
		console.error
	);
```

上面代码中，po的状态一旦变为resolved，就一次调用后面每一个then指定的回调函数，每一步都必须等到前一步完成，才会执行。

Promise对象的错误有传递性。

#### Promise对象的生成

ES6提供了原生的Promise构造函数，用来生成Promise实例。

```
var promise = new Promise(function(resolve,reject){
  // 异步操作的代码
  
  if(/* 异步操作成功 */){
    resolve(value);
  }else{
    reject(error);
  }
});
```

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们时两个函数，由JS引擎提供，不用自己部署。

## 严格模式

进入“严格模式”的标志，是一行字符串`use strict`。

1. 针对整个脚本文件

   将`use strict`放在脚本文件第一行，整个脚本都将以“严格模式”运行。

2. 针对单个函数

   `use strict`放在函数体的第一行，整个函数以“严格模式”运行。

3. 脚本文件的变通写法

   两个不同模式的脚本合并成一个文件，如果严格模式的脚本在前，则合并后的脚本都是“严格模式”；如果正常模式的脚本在前，则合并后的脚本都是“正常模式”。总之，这两种合并后的结果都是不正确的。

   正确的做法是，使用前面第二种方法，将整个脚本文件放在一个立即执行的匿名函数之中。

   ```
   (function(){
     'use strict';
     // some code here
   })();
   ```

   ​

# DOM模型

DOM是JS操作网页的接口，它的作用是将网页转为一个JS对象，从而可以用脚本进行各种操作。

## 事件模型

### 事件的传播

#### 传播的三个阶段

当一个事件发生以后，它会在不同的DOM节点之间传播（propagation）。这种传播分成三个阶段：

+ 第一阶段：从window对象传导到目标节点，称为“捕获阶段”。
+ 第二阶段：从目标节点上触发，称为“目标阶段”。
+ 第三阶段：从目标节点传导回window对象，称为“冒泡阶段”。

这三种阶段的传播模型，会使得一个事件在多个节点上触发。比如，假设点击`<div>`之中嵌套一个`<p>`节点：

```
<div>
	<p>Click Me</p>
</div>
```

如果对这两个节点的click事件都设定监听函数，则click事件会被触发四次：

+ 捕获阶段：事件从`<div>`向`<p>`传播时，触发`<div>`的`click`事件；
+ 目标阶段：事件从`<div>`到达`<p>`时，触发`<p>`的`click`事件；
+ 目标阶段：事件离开`<p>`时，触发`<p>`的`click`事件；
+ 冒泡阶段：事件从`<p>`传回`<div>`时，再次触发`<div>`的`click`事件。

#### 事件的代理

由于事件会在冒泡阶段向上传播的父节点，因此可以把子节点的监听函数定义在父节点上，由父节点的监听函数统一处理多个子元素的事件。这种方法叫做事件的代理。

如果希望事件到某个节点为止，不再传播，可以使用时间对象的`stopPropagation`方法。

`stopPropagation`方法只会阻止当前监听函数的传播，不会阻止节点上的其他click事件的监听函数。如果想要不再触发那些监听函数，可以使用stopImmediatePropagation方法。

### Event对象

事件发生以后，会生成一个事件对象，**作为参数传给监听函数**。浏览器原生提供一个Event对象，所有的事件都是这个对象的实例。

#### event.currentTarget

currentTarget属性返回事件当前所在的节点，即正在执行的监听函数所绑定的那个节点。作为比较，target属性返回事件发生的节点。如果监听函数在捕获阶段和冒泡阶段触发，那么这两个属性返回的值是不一样的。

#### event.target

target属性返回触发事件的那个节点，即事件最初发生的节点。如果监听函数不在该即诶单触发，那么它与currentTarget属性返回的值是不一样的。

```
function hide(e){
  console.log(this === e.target);
  e.target.style.visibility = "hidden";
}

// HTML代码为
// <p id="para">Hello <em>World</em></p>
para.addEventListener('click',hide,false);
```

上面代码中，如果在para节点的em子节点上面点击，则e.target指向em子节点，导致em子节点（即World部分）会不可见，且输出false。

**事件的触发，和事件的监听不是一个概念**，比如上面代码中，点击em节点，click事件是在em节点触发的，然后按照事件传播模型从顶层window节点开始向下捕获，然后再从目标节点em节点向上冒泡，在冒泡阶段，p节点上监听了click事件，那么会执行p节点的监听函数，但此时event.target指向的是触发click事件的em节点，虽然监听函数是在p节点上执行的。

#### event.preventDefault()

preventDefault方法取消浏览器对当前事件的默认行为。该方法生效的前提是，事件的cancelable属性为true。

该方法不会阻止事件的进一步传播。只要在事件的传播过程中（捕获阶段、目标阶段、冒泡阶段皆可），使用了preventDefault方法，该事件的默认方法就不会执行。

#### event.stopPropagation()

stopPropagation方法阻止事件在DOM中继续传播，防止再触发定义在别的节点上的监听函数，但是不包括在当前节点上新定义的事件监听函数。

**stopPropagation既可以阻止事件冒泡，也可以阻止事件捕获，也可以阻止事件目标阶段。**

#### event.stopImmediatePropagation()

stopImmediatePropagation方法阻止同一个事件的其他监听函数被调用。

如果同一个节点对于同一个事件指定了多个监听函数，这些函数会根据添加的顺序一次调用。只要其中一个监听函数调用了stopImmediatePropagation方法，其他监听函数就不会再执行了。

# 浏览器环境

## 概述

### JS代码嵌入网页的方法

#### script标签：代码嵌入网页

#### script标签：加载外部脚本

#### 事件属性

某些HTML元素的事件属性（比如onclic和onmouseover），可以写入JS代码。当指定事件发生时，就会调用这些代码。

```
<div onclick="alert('Hello')"></div>
```

上面的事件属性代码只有一个语句。如果有多个语句，用分号分隔即可。

#### URL协议

URL支持`javascript:`协议，调用这个URL时，就会执行JS代码。

```
<a href="javascript:alert('Hello')"></a>
```

### script标签

#### 工作原理

正常的网页加载流程是这样的：

+ 浏览器一边下载HTML网页，一边开始解析
+ 解析过程中，发现`<script>`标签
+ 暂停解析，网页渲染的控制权转交给JavaScript引擎
+ 如果`<script>`标签引用了外部脚本，就下载该脚本，否则就直接执行
+ 执行完毕，控制权交还渲染引擎，恢复往下解析HTML网页

### 浏览器的组成

浏览器的核心是两部分：渲染引擎和JavaScript解释器（又称JavaScript引擎）。

#### 渲染引擎

渲染引擎处理网页，通常分成四个阶段：

+ 解析代码：HTML代码解析为DOM，CSS代码解析为CSSOM（CSS Object Model）
+ 对象合成：将DOM和CSSOM合成一个渲染树
+ 布局：计算出渲染树的布局（layout）
+ 绘制：将渲染树绘制到屏幕

以上四步并非严格按顺序执行，往往第一步还没完成，第二步和第三步就已经开始了。

#### JavaScript引擎

JS引擎的主要作用是，读取网页中的JS代码，对其处理后运行。

## window对象

### window对象

在浏览器中，window对象指当前的浏览器窗口。它也是所有对象的顶层对象。

JS规定，浏览器环境的所有全局变量，都是window对象的属性。

### history对象

浏览器窗口有一个history对象，用来保存浏览历史。

### Cookie

Cookie时服务器保存在浏览器的一小段文本信息，每个Cookie的大小一般不能超过4KB。浏览器每次向服务器发出请求，就会自动附上这段信息。

### Web Storage：浏览器端数据储存机制

这个API的作用是，使得网页可以在浏览器端储存数据。它分成两类：sessionStorage和localStorage。

sessionStorage保存的数据用于浏览器的一次会话，当会话结束（通常是该窗口关闭），数据被清空；localStorage保存的数据长期存在，下一次访问该网站的时候，网页可以直接读取以前保存的数据。

每个域名的存储上限视浏览器而定。Chrome是2.5MB，Firefox和Opera是5MB，IE是10MB。

另外，与Cookie一样，它们也受同域限制。某个网页存入的数据，只有同域下的网页才能读取。

#### 存入／读取数据

存入数据使用setItem方法：

```
sessionStorage.setItem("key","value");
localStorage.setItem("key","value");
```

读取数据使用getItem方法：

```
var valueSession = sessionStorage.getItem("key");
var valueLocal = localStorage.getItem("key");
```

#### 清除数据

removeItem方法用于清除某个键名对应的数据。

```
sessionStorage.removeItem('key');
localStorage.removeItem('key');
```

clear方法用于清除所有保存的数据。

```
sessionStorage.clear();
localStorage.clear();
```

## 同源政策

### 含义

1995年，同源政策由Netscape公司引入浏览器。

最初，它的含义是指，A网页设置的Cookie，B网页不能打开，除非这两个网页“同源”。所谓“同源”指的是“三个相同”。

+ 协议相同
+ 域名相同
+ 端口相同

## AJAX

浏览器与服务器之间，采用HTTP协议通信。用户在浏览器地址栏键入一个网址，或者通过网页表单向服务器提交内容，这时浏览器就会想服务器发出HTTP请求。

AJAX包括以下几个步骤：

1. 创建AJAX对象
2. 发出HTTP请求
3. 接受服务器传回的数据
4. 更新网页数据

概括起来就是一句话，AJAX通过原生的XMLHttpRequest对象发出HTTP请求，得到服务器返回的数据后，再进行处理。

```
var xhr = new XMLHttpRequest();

// 指定通信过程中状态改变时的回调函数
xhr.onreadtstatechange = function(){
	// 通信成功时，状态值为4
  if(xhr.readyState === 4){
    if(xhr.status === 200){
      console.log(xhr.resonseText);
    }else{
      console.log(xhr.statusText);
    }
  }
}

xhr.onerror = function (e){
  console.log(xhr.statusText);
}

// open方式用于指定HTTP动词、请求的网址、是否异步
xhr.open('GET','/endpoint',true);

// 发送HTTP请求
xhr.send(null);
```

