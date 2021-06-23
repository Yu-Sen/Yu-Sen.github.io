---
title: 【数据分析】第五阶段：python（一）
date: 2021-06-10 20:32:47
tags:
- 数据分析
- python
categories:
- 学习笔记
description: 本模块全面系统地讲授Python语法基础，面向对象，高阶函数，模块化运用。为后面的数据分析编程打下基础，同时具备自动化处理数据的基础。
---

# 变量

## 变量名

字母、数字、下划线

数字不能作为开头

区分大小写

驼峰命名法

避开关键字

## 声明，赋值

```python
>>> x="helloWorld"
>>> print(x)
helloWorld

>>> a=b=c=1
>>> print(a,b,c)
1 1 1

>>> a,b,c = 1,2,3
>>> print(a,b,c)
1 2 3

>>> a,b = 1,2
>>> print(a,b)
1 2
>>> a,b = b,a
>>> print(a,b)
2 1

# 变量销毁
-- python自动销毁，内存回收
-- del手动销毁变量
>>> x
'helloWorld'
>>> del x
>>> x
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'x' is not defined
```

# 数据类型

## 数据类型

python也是弱类型语言，和js一样，声明变量时不需要制定数据类型。

数值

- 整型

- 浮点型

```python
# 只要有小数点就是浮点型，并且浮点型参与的运算，结果也一定是浮点型
>>> a=1
>>> b=10.0
>>> a+b
11.0
```

字符串

```python
>>> print('"打印双引号"')
"打印双引号"

>>> print("\"转义\"")
"转义"

>>> """这是一段
... 有换行的
... 长文本"""
'这是一段\n有换行的\n长文本'

>>> '''这是一段
... 有换行的
... 长文本'''
'这是一段\n有换行的\n长文本'
```

布尔值

None

## 数据类型转换

转布尔：bool()

下面基本完整地列出了会被视为假值的内置对象:

- 被定义为假值的常量: `None` 和 `False`。
- 任何数值类型的零: `0`, `0.0`, `0j`, `Decimal(0)`, `Fraction(0, 1)`
- 空的序列和多项集: `''`, `()`, `[]`, `{}`, `set()`, `range(0)`

```python
#数字转bool
>>> bool(1)
True
>>> bool(-1)
True
>>> bool(1.0)
True
>>> bool(0.00001)
True
>>> bool(0)
False
>>> bool(0.0)
False

#字符串转bool
>>> bool("aaa")
True
>>> bool("")
False
>>> bool(" ")
True

#None转bool
>>> bool(None)
False

>>> bool([])
False
>>> bool(tuple())
False
```

转数值：int()、float()

```python
#数字字符串转整型，可以带正负号，两边可以有空格，不能有其他字符
#js中1+"1"="11"
>>> 1+"1"
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
>>> int("1")
1
>>> 1+int("1")
2
>>> int(" 100 ")
100
>>> int("-100")
-100

#数字字符串转浮点型，可以带正负号，两边可以有空格，不能有其他字符
>>> float("3.14")
3.14
>>> float("aaa3.14")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: could not convert string to float: 'aaa3.14'
>>> float("  3.14  ")
3.14

#整型、浮点型互转，不会四舍五入，只截取整数部分
>>> int(3.14)
3
>>> float(10)
10.0
>>> int(9.9)
9
```

# 运算符

## 算术运算符

```python
加 +，减 -，乘 *，指数运算**(js也有)，除 /，整除 //，取模运算 %

# python也有精度问题
>>> 0.1+0.2
0.30000000000000004

# 指数（index）是幂运算aⁿ(a≠0)中的一个参数，a为底数，n为指数
>>> 2**3
8

# / 只返回浮点型，即使能整除
>>> 2/1
2.0
>>> 3/2
1.5

# // 只返回整数部分（不会四舍五入），即使不能整除
>>> 2//1
2
>>> 3//2
1

# % 取模运算
>>> 4 % 2
0
>>> 5 % 2
1
>>> 3 % 4
3
>>> -3 % 4 # 当有负数的时候和js中不一样，有负数时a % b = a - (a//b)*b
1
>>> 3 % -4
-1

# 0 不能作为除数
>>> 1 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
>>> 1 // 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
>>> 1 % 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: integer division or modulo by zero
```

### 取模和取余

a ➗ b，a是被除数，b是除数

余数，是指除法中不能整除余留的部分
1%2 = 0 余 1
因为 1 小于 2 所以 不能整除 2 ，所以不能参与整除的部分（余数）还是 1

取模和求余应该是同一种运算，只是在被除数和除数符号不同时，余数的符号是有歧义的，可为正也可为负。

不同的编程语言对此有不同的处理。

C语言中取模运算 % 结果与**被除数**同符号。大部分编程语言都是这样，比如Go，C#，Java，Rust，Swift，JavaScript，PHP。

Python 中取模运算 % 结果与**除数**同符号。同时Python中函数 math.fmod 提供了与C语言中一致的取模运算。

```python
# python
>>> 3 % 4
3
>>> -3 % 4 # 有负数时a % b = a - (a//b)*b
1
>>> 3 % -4
-1
```

```js
# js
3 % 4 = 3
-3 % 4 = -3
3 % -4 = 3
```

### 字符串的拼接+、重复 *

```python
>>> "hello" + "world"
'helloworld'

>>> "hello"*5
'hellohellohellohellohello'

# python中 字符串+数值 不能直接转字符串，要用str()函数转为字符串。和js区别
>>> "age:" + 18
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
>>> "age:" + str(18)
'age:18'
```

### python中没有++，--

## 赋值运算符

=，+=，-=，*=，/=，//=，%=，**=(js也有)

```python
# 幂（power）是指数运算的结果。当m为正整数时，nᵐ指m个n相乘。把nᵐ看作乘方的结果，叫做n的m次幂，也叫n的m次方。
>>> a = 2
>>> a **= 3
>>> a
8
```

## 比较运算符

==，!=，>=，<=，>，<

没有js的===

```python
>>> 1==1
True
>>> 1==1.0
True
>>> 1=='1'
False
>>> 1===1
  File "<stdin>", line 1
    1===1
       ^
SyntaxError: invalid syntax
>>>
```

运算符 [`in`](https://docs.python.org/zh-cn/3/reference/expressions.html#in) 和 [`not in`](https://docs.python.org/zh-cn/3/reference/expressions.html#not-in) 用于成员检测。 如果 *x* 是 *s* 的成员则 `x in s` 求值为 `True`，否则为 `False`。 `x not in s` 返回 `x in s` 取反后的值。 所有内置序列和集合类型以及字典都支持此运算，对于字典来说 `in` 检测其是否有给定的键。 对于 list, tuple, set, frozenset, dict 或 collections.deque 这样的容器类型，表达式 `x in y` 等价于 `any(x is e or x == e for e in y)`。

对于字符串和字节串类型来说，当且仅当 *x* 是 *y* 的子串时 `x in y` 为 `True`。 一个等价的检测是 `y.find(x) != -1`。 空字符串总是被视为任何其他字符串的子串，因此 `"" in "abc"` 将返回 `True`。

## 逻辑运算符

and，or，not

不是js中的&&，||，!

```python
>>> a = 10
>>> a == 1 and a == 10
False
>>> a ==1 or a == 10
True
>>> not a == 10
False

# 隐式转换
>>> not 1
False
>>> not 0
True

# and，or编译过程，and一直编译到最后，or编译到true就不再继续执行
>>> a = 1 and 0
>>> a
0
>>> a = 0 and 1
>>> a
0
>>> a = 1 and 2
>>> a
2
>>> a = 2 and 1
>>> a
1
>>> a = 1 or 0
>>> a
1
>>> a = 0 or 1
>>> a
1
>>> a = 1 or 2
>>> a
1
>>> a = 2 or 1
>>> a
2
```

# 条件语句

缩进代替{ }，elif代替else if，条件语句不用( )，后面跟 :

```python
if 条件1:
  ...
elif 条件2:
  ...
else:
  ...

# 嵌套
if 条件1:
  if 条件2:
    ...
  else:
    ...
else:
  ...
```

## pass占位语句

仅占位

```python
if 条件1:
	pass
else:
  ...
```

## 0<a<10

python中可以这样写，其他语言只能 a>0 && a<10

# 循环语句

## while循环

```python
>>> a = 0
>>> while a < 5 :
...     a += 1
...     print(a)
...
1
2
3
4
5
```

## for in循环

```python
>>> a = "hello"
>>> for i in a:
...     print(i)
...
h
e
l
l
o

>>> for i in range(5):
...     print(i)
...
0
1
2
3
4

>>> for a in range(2):
...     for b in range(5):
...             print(b+1,end="")
...     print()
...
12345
12345

# class range(stop)
# class range(start, stop[, step])
# range 构造器的参数必须为整数（可以是内置的 int 或任何实现了 __index__ 特殊方法的对象）。 
# 如果省略 step 参数，其默认值为 1。 
# 如果省略 start 参数，其默认值为 0，
# 如果 step 为零则会引发 ValueError。
>>> for x in range(1,10):
...     for y in range(1,x+1):
...             print(str(y)+'x'+str(x)+'='+str(x*y),end=' ')
...     print()
...
1x1=1
1x2=2 2x2=4
1x3=3 2x3=6 3x3=9
1x4=4 2x4=8 3x4=12 4x4=16
1x5=5 2x5=10 3x5=15 4x5=20 5x5=25
1x6=6 2x6=12 3x6=18 4x6=24 5x6=30 6x6=36
1x7=7 2x7=14 3x7=21 4x7=28 5x7=35 6x7=42 7x7=49
1x8=8 2x8=16 3x8=24 4x8=32 5x8=40 6x8=48 7x8=56 8x8=64
1x9=9 2x9=18 3x9=27 4x9=36 5x9=45 6x9=54 7x9=63 8x9=72 9x9=81
```

### print()输出在一行

```python
print(i,end="") # end参数指定结尾的分隔符，默认是换行符\t
```

## continue、break

```python
# continue
>>> a = 0
>>> while a < 5:
...     a+=1
...     if a == 2:
...             print('跳过2继续')
...             continue
...     print(a)
...
1
跳过2继续
3
4
5

# break
>>> a = 0
>>> while a < 5:
...     a += 1
...     if a == 3:
...             print('到3停止')
...             break
...     print(a)
...
1
2
到3停止
```

# 数据结构

## 文本序列类型-字符串str

在 Python 中处理文本数据是使用 [`str`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str)象，也称为 *字符串*。 字符串是由 Unicode 码位构成的不可变 [序列](https://docs.python.org/zh-cn/3/library/stdtypes.html#typesseq)

### 占位符

- %d：整数占位符
- %f：浮点数占位符
- %.f：指定精度的浮点数占位符
- %s：字符串占位符
- %%：占位符后输出%，避免和占位符的%产生歧义

```python
# 整数%d，值只能是数值、布尔值，浮点型只截取整数部分，不四舍五入
>>> print("%d" % 3)
3
>>> print("%d" % 3.14)
3
>>> print("%d" % 3.5)
3
>>> print("%d" % "3")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: %d format: a number is required, not str
>>> print("%d" % True)
1
>>> print("%d" % False)
0
>>> print("%d" % None)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: %d format: a number is required, not NoneType

# 浮点数%f，值只能是数值，
>>> print("%f" % 3.14)
3.140000
>>> print("%f" % 3)
3.000000
>>> print("%f" % "3")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be real number, not str
>>> print("%f" % "3.14")
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be real number, not str
>>> print("%f" % True)
1.000000
>>> print("%f" % False)
0.000000
>>> print("%f" % None)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be real number, not NoneType

# 指定精度浮点数%.f，值只能是数值、布尔值，位数不足自动补0，位数超出四舍五入
>>> print("%.2f" % 3)
3.00
>>> print("%.2f" % 3.14)
3.14
>>> print("%.2f" % 3.145)
3.15
>>> print("%.2f" % 3.1)
3.10
>>> print("%.2f" % True)
1.00
>>> print("%.2f" % False)
0.00
>>> print("%.2f" % None)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: must be real number, not NoneType

# 字符串%s，值可以是字符串、数值、布尔值、None，都被转为字符串
>>> print("%s" % "3.14")
3.14
>>> print("%s" % 3.14)
3.14
>>> print("%s" % 3)
3
>>> print("%s" % True)
True
>>> print("%s" % False)
False
>>> print("%s" % None)
None

# 占位符后想输出%
>>> print("%d%" % 3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: incomplete format
>>> print("%d%%" % 3)
3%

# 占位符混合使用
>>> "%d年%s公司营收增长了%.2f%%" % (2019, "腾讯", 20.28)
'2019年腾讯公司营收增长了20.28%'
```

### format()函数

```python
# str.format(*args, **kwargs)
# 执行字符串格式化操作。 调用此方法的字符串可以包含字符串字面值或者以花括号 {} 括起来的替换域。 
# 每个替换域可以包含一个位置参数的数字索引，或者一个关键字参数的名称。 
# 返回的字符串副本中每个替换域都会被替换为对应参数的字符串值。
>>> '{:,}'.format(1234567890)
'1,234,567,890'

>>> "{}年{}公司营收增长了{}%".format(2019,"腾讯",20.28)
'2019年腾讯公司营收增长了20.28%'
>>> "{0}年{1}公司营收增长了{2}%".format(2019,"腾讯",20.28)
'2019年腾讯公司营收增长了20.28%'
>>> "{2}年{1}公司营收增长了{0}%".format(2019,"腾讯",20.28)
'20.28年腾讯公司营收增长了2019%'

>>> "{0}的GDP为{1:,}...虽然他的GDP只有{1:,}美元，但他的人均GDP高达18万美元".format("摩纳哥",70000000)
'摩纳哥的GDP为70,000,000...虽然他的GDP只有70,000,000美元，但他的人均GDP高达18万美元'
```

### 字符串索引和切片

![](Xnip2021-06-12_15-13-43)

```python
>>> s = 'china'
>>> s[0]
'c'
>>> s[2]
'i'
>>> s[4]
'a'
>>> s[5]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
>>> s[-1]
'a'
>>> s[-3]
'i'
>>> s[-5]
'c'
>>> s[-6]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
```

```python
#[开始索引:结束索引:步长]，结束索引的字符不包含在内，步长为负数表示倒序输出
>>> s = 'china'
>>> s[0:0]
''
>>> s[0:1]
'c'
>>> s[0:3]
'chi'
>>> s[0:4]
'chin'
>>> s[0:5]
'china'
>>> s[0:6]
'china'
>>> s[0:60]
'china'
>>> s[:3]
'chi'
>>> s[0:]
'china'
>>> s[:]
'china'
>>> s[::1]
'china'
>>> s[::2]
'cia'
>>> s[::3]
'cn'
>>> s[::-1]
'anihc'
>>> s[::-2]
'aic'
```

### str类函数

```python
# str.strip([chars])
# 返回原字符串的副本，移除其中的前导和末尾字符。 chars 参数为指定要移除字符的字符串。 
# 如果省略或为 None，则 chars 参数默认移除空白符。 实际上 chars 参数并非指定单个前缀或后缀；而是会移除参数值的所有组合
>>> ' abc '.strip()
'abc'
>>> '\tabc\n'.strip()
'abc'
>>> '\ta b\tc\nd\n'.strip()
'a b\tc\nd'
>>> '   spacious   '.strip()
'spacious'
>>> 'www.example.com'.strip('cmowz.')
'example'

# str.upper()、lower()大小写转换
# 返回原字符串的副本，其中所有区分大小写的字符均转换为大写/小写。 
# 请注意如果 s 包含不区分大小写的字符或者如果结果字符的 Unicode 类别不是 "Lu" (Letter, uppercase) 
# 而是 "Lt" (Letter, titlecase) 则 s.upper().isupper() 有可能为 False。
>>> 'aBc'.upper()
'ABC'
>>> 'AbC'.lower()
'abc'

# str.capitalize()
# 返回原字符串的副本，其首个字符大写，其余为小写
>>> 'abc'.capitalize()
'Abc'

# str.title()
# 返回原字符串的标题版本，其中每个单词第一个字母为大写，其余字母为小写
>>> 'i have a dream'.title()
'I Have A Dream'

# str.isupper(),str.islower()
# 如果字符串中至少有一个区分大小写的字符, 且此类字符均为大写/小写则返回 True ，否则返回 False 
>>>'BANANA'.isupper()
True
>>>'banana'.isupper()
False
>>>'baNana'.isupper()
False
>>>' '.isupper()
False

# str.isdigit()
# 如果字符串中的所有字符都是数字，并且至少有一个字符，返回 True ，否则返回 False 
>>> '1234'.isdigit()
True
>>> 'a1234'.isdigit()
False
>>> ' '.isdigit()
False
>>> ''.isdigit()
False

# str.startswith(prefix[, start[, end]])
# str.endswith(suffix[, start[, end]])
# 如果字符串以指定的 prefix 开始则返回 True，否则返回 False。 
# prefix 也可以为由多个供查找的前缀构成的元组。 
# 如果有可选项 start，将从所指定位置开始检查。 如果有可选项 end，将在所指定位置停止比较
>>> 'abc'.startswith('a')
True
>>> 'abc'.startswith('ab')
True
>>> 'abc'.startswith('a',1)
False

# str.find(sub[, start[, end]])
# 返回子字符串 sub 在 s[start:end] 切片内被找到的最小索引。 
# 可选参数 start 与 end 会被解读为切片表示法。 如果 sub 未被找到则返回 -1。
>>> 'python'.find('th')
2
>>> 'python'.find('thh')
-1
#  find() 方法应该只在你需要知道 sub 所在位置时使用。 要检查 sub 是否为子字符串，请使用 in 操作符:
>>> 'th' in 'python'
True
# str.index(sub[, start[, end]])
# 类似于 find()，但在找不到子类时会引发 ValueError
>>> 'python'.index('th')
2
>>> 'python'.index('thh')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: substring not found
  
# str.count(sub[, start[, end]])
# 返回子字符串 sub 在 [start, end] 范围内非重叠出现的次数。 
# 可选参数 start 与 end 会被解读为切片表示法
>>> 'banana'.count('a')
3
>>> 'banana'.count('a',2)
2
>>> 'banana'.count('a',2,4)
1

# str.replace(old, new[, count])
# 返回字符串的副本，其中出现的所有子字符串 old 都将被替换为 new。 
# 如果给出了可选参数 count，则只替换前 count 次出现
>>> 'banana'.replace('a','k')
'bknknk'
>>> 'banana'.replace('a','k',2)
'bknkna'
>>> 'apple orange'.replace('apple','banana')
'banana orange'

# str.zfill(width)¶
# 返回原字符串的副本，在左边填充 ASCII '0' 数码使其长度变为 width。 
# 正负值前缀 ('+'/'-') 的处理方式是在正负符号 之后 填充而非在之前。 
# 如果 width 小于等于 len(s) 则返回原字符串的副本。
>>> "42".zfill(5)
'00042'
>>> "-42".zfill(5)
'-0042'
```

```python
# len(s)
# 返回对象的长度（元素个数）。
# 实参可以是序列（如 string、bytes、tuple、list 或 range 等）
# 或集合（如 dictionary、set 或 frozen set 等）
>>> len('banana')
6
>>> len('')
0
>>> len(' ')
1
```

## 序列类型-元组tuple

元组中的元素可以是任意类型，而字符串中的元素只能是字符

元组是不可变序列，通常用于储存异构数据的多项集

### 构建元组

```python
class tuple([iterable])
可以用多种方式构建元组：

使用一对圆括号来表示空元组: ()
使用一个后缀的逗号来表示单元组: a, 或 (a,)
使用以逗号分隔的多个项: a, b, c or (a, b, c)
使用内置的 tuple(): tuple() 或 tuple(iterable)

构造器将构造一个元组，其中的项与 iterable 中的项具有相同的值与顺序。 
iterable 可以是序列、支持迭代的容器或其他可迭代对象。

请注意决定生成元组的其实是逗号而不是圆括号。 圆括号只是可选的

# 使用内置的 tuple()
>>> a = tuple('python')
>>> a
('p', 'y', 't', 'h', 'o', 'n')

# 使用一对圆括号来表示空元组
>>> b = ('p','y','t','h','o','n')
>>> b
('p', 'y', 't', 'h', 'o', 'n')

# 元组实现了所有 一般 序列的操作
>>> print(b[0],b[2],b[-1])
p t n

# 元组是不可变序列
>>> b[0] = 'a'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
  
# 元组可以存储不同类型
>>> c = ('my','age','is',18)
>>> c
('my', 'age', 'is', 18)

# 元组实现了所有 一般 序列的操作
>>> print(c[:2])
('my', 'age')

# 使用以逗号分隔的多个项
>>> d = 'p','y','t','h','o','n'
>>> d
('p', 'y', 't', 'h', 'o', 'n')

# 使用一个后缀的逗号来表示单元组
>>> e = ('my',)
>>> e
('my',)
>>> e = 'my',
>>> e
('my',)
>>> e = ('my')
>>> e
'my' #字符串
```

### 元组操作

```python
# str.join(iterable)
# 返回一个由 iterable 中的字符串拼接而成的字符串。 
# 如果 iterable 中存在任何非字符串值包括 bytes 对象则会引发 TypeError。 
# 调用该方法的字符串将作为元素之间的分隔

>>> e = 'my','age','is',18
>>> ' '.join(e)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: sequence item 3: expected str instance, int found
    
>>> e = 'my','age','is','18'
>>> ' '.join(e)
'my age is 18'

# 元组使用count(),index(),in
>>> a = 'b','a','n','a','n','a'
>>> a.count('a')
3
>>> a.index('a')
1
>>> len(a)
6
>>> 'b' in a
True

# for in遍历元组元素
>>> for i in a:
...     print(i,end = ',')
...
b,a,n,a,n,a,
```

### 练习

```python
# 员工季度销售数据，姓名，第一季度每个月销售额
sales = (
    ("Peter", (78, 70, 65)),
    ("John", (88, 80, 85)),
    ("Tony", (90, 99, 95)),
    ("Henry", (80, 70, 55)),
    ("Mike", (95, 90, 95)),
)
# 找出第一季度销售总额最高的员工，并展示该员工姓名和销售总额
name = ''
max_sales = 0
for el in sales:
    total_sales = 0
    for s in el[1]:
        total_sales += s
    if total_sales >= max_sales:
        max_sales = total_sales
        name = el[0]
    print(el[0],total_sales)
print('销售冠军：',name,max_sales)

Peter 213
John 253
Tony 284
Henry 205
Mike 280
销售冠军 Tony 284

# 优化代码
# sum(iterable, /, start=0)
# 从 start 开始自左向右对 iterable 的项求和并返回总计值。 
# iterable 的项通常为数字，而 start 值则不允许为字符串。
name = ''
max_sales = 0
for el in sales:
    total_sales = sum(el[1])
    if total_sales >= max_sales:
        name, max_sales = el[0], total_sales
    print(el[0], total_sales)
print('销售冠军', name, max_sales)
```

## 序列类型-列表list

列表是可变序列，通常用于存放同类项目的集合（其中精确的相似程度将根据应用而变化）。

列表中的元素可以是不同类型。

### 构建列表

```python
class list([iterable])
可以用多种方式构建列表：

使用一对方括号来表示空列表: []
使用方括号，其中的项以逗号分隔: [a], [a, b, c]
使用列表推导式: [x for x in iterable]
使用类型的构造器: list() 或 list(iterable)
  
构造器将构造一个列表，其中的项与 iterable 中的项具有相同的的值与顺序。
iterable 可以是序列、支持迭代的容器或其它可迭代对象

>>> lst = list()
>>> lst
[]
>>> lst = []
>>> lst
[]
>>> lst = [1,2,3,4]
>>> lst
[1, 2, 3, 4]
>>> lst = ['a',1,2,3.14,('b',4,'c')]
>>> lst
['a', 1, 2, 3.14, ('b', 4, 'c')]

>>> lst = list('abcde')
>>> lst
['a', 'b', 'c', 'd', 'e']
>>> lst = list(range(5))
>>> lst
[0, 1, 2, 3, 4]
>>> lst = list((1,2,3,'a','b','c'))
>>> lst
[1, 2, 3, 'a', 'b', 'c']
```

### 列表操作

```python
>>> lst = list('abcde')

# for in遍历列表
>>> for i in lst:
...     print(i,end=',')
...
a,b,c,d,e,

>>> lst.count('a')
1
>>> lst.index('a')
0
>>> len(lst)
5

>>> lst.insert(0,'A')
>>> lst
['A', 'a', 'b', 'c', 'd', 'e']
>>> lst.insert(-1,'E')
>>> lst
['A', 'a', 'b', 'c', 'd', 'E', 'e']

>>> lst.pop()
'e'
>>> lst
['A','a', 'b', 'c', 'd', 'E']
>>> lst.pop(0)
'A'
>>> lst
['a', 'b', 'c', 'd', 'E']
>>> lst.remove('E')
>>> lst
['a', 'b', 'c', 'd']
>>> lst.append('c')
>>> lst
['a', 'b', 'c', 'd', 'c']
>>> lst.remove('c')
>>> lst
['a', 'b', 'd', 'c']
>>> lst.clear()
>>> lst
[]
>>> lst.extend(['a','b','c'])
>>> lst
['a', 'b', 'c']
>>> lst.reverse()
>>> lst
['c', 'b', 'a']
```

### 列表sort()方法

`sort`(*, *key=None*, *reverse=False*)

此方法会对列表进行原地排序，只使用 `<` 来进行各项间比较。 异常不会被屏蔽 —— 如果有任何比较操作失败，整个排序操作将失败（而列表可能会处于被部分修改的状态）。

*key* 指定带有一个参数的函数，用于从每个列表元素中提取比较键 (例如 `key=str.lower`)。 对应于列表中每一项的键会被计算一次，然后在整个排序过程中使用。 默认值 `None` 表示直接对列表项排序而不计算一个单独的键值。

*reverse* 为一个布尔值。 如果设为 `True`，则每个列表元素将按反向顺序比较进行排序。

```python
>>> lst = [1,3,53,78,23,68,69,34]
>>> lst.sort()
>>> lst
[1, 3, 23, 34, 53, 68, 69, 78]
>>> lst.sort(reverse=True)
>>> lst
[78, 69, 68, 53, 34, 23, 3, 1]

>>> revenue = [('1月',561000),('2月',485000),('3',622000)]
>>> revenue.sort(key=lambda x: x[1], reverse = True)
>>> revenue
[('3', 622000), ('1月', 561000), ('2月', 485000)]

# 字符串按首字符ascii码大小排序，如果相同再按第二个字符ascii码排序，以此类推
>>> lst = ['apple','banana','orange','blueberry']
>>> lst.sort()
>>> lst
['apple', 'banana', 'blueberry', 'orange']

# ord(c)
# 对表示单个 Unicode 字符的字符串，返回代表它 Unicode 码点的整数。
# 例如 ord('a') 返回整数 97， ord('€') （欧元符号）返回 8364 。这是 chr() 的逆函数。
# chr(i)
# 返回 Unicode 码位为整数 i 的字符的字符串格式。
# 例如，chr(97) 返回字符串 'a'，chr(8364) 返回字符串 '€'。这是 ord() 的逆函数。
```

Python 列表有一个内置的 `list.sort()`方法可以直接修改列表。还有一个 `sorted()`内置函数，它会从个可迭代对象构建一个新的排序列表。

### 练习

```python
# 按销售总额从高到低排序，展示每个人姓名和销售业绩
sales = (
    ("Peter", (78, 70, 65)),
    ("John", (88, 80, 85)),
    ("Tony", (90, 99, 95)),
    ("Henry", (80, 70, 55)),
    ("Mike", (95, 90, 95)),
)
lst = [i+(sum(i[1]),) for i in sales]
lst.sort(key=lambda x: x[2], reverse=True)
print(lst)

[
  ('Tony', (90, 99, 95), 284), 
  ('Mike', (95, 90, 95), 280), 
  ('John', (88, 80, 85), 253), 
  ('Peter', (78, 70, 65), 213), 
  ('Henry', (80, 70, 55), 205)
]
```

## 推导式

为了构建列表、集合或字典，Python 提供了名为“显示”的特殊句法，每个类型各有两种形式:

- 第一种是显式地列出容器内容，比如`lst = [1, 2, 3]`。
- 第二种是通过一组循环和筛选指令计算出来，称为 *推导式*。

推导式的结构是一个单独表达式后面加至少一个 `for` 子句以及零个或更多个 `for` 或 `if` 子句。

例如: `[x*y for x in range(10) for y in range(x, x+10)]`。

```python
>>> lst = list(range(10))
>>> lst
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

>>> lst = [i for i in range(10)]
>>> lst
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
# 相当于js中，通过for循环向数组添加元素，来构建数组
>>> lst = []
>>> for i in range(10):
...     lst.append(i)
>>> lst
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

>>> lst = [i for i in range(10) if i % 2 == 0]
>>> lst
[0, 2, 4, 6, 8]
>>> lst = [i*2 for i in range(5)]
>>> lst
[0, 2, 4, 6, 8]

# 打印出26个大写字母
>>> ord('A')
65
>>> A_Z = [chr(i) for i in range(65,65+26)]
>>> A_Z
['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
```

## 序列类型-range对象

[`range`](https://docs.python.org/zh-cn/3/library/stdtypes.html#range)数字序列，通常用于在 [`for`](https://docs.python.org/zh-cn/3/reference/compound_stmts.html#for)中循环指定的次数

*class* `range`(*stop*)

*class* `range`(*start*, *stop*[, *step*])

range 构造器的参数必须为整数（可以是内置的 [`int`](https://docs.python.org/zh-cn/3/library/functions.html#int)何实现了 `__index__` 特殊方法的对象）。 如果省略 *step* 参数，其默认值为 `1`。 如果省略 *start* 参数，其默认值为 `0`，如果 *step* 为零则会引发 [`ValueError`](https://docs.python.org/zh-cn/3/library/exceptions.html#ValueError)

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>>> list(range(0, 30, 5))
[0, 5, 10, 15, 20, 25]
>>> list(range(0, 10, 3))
[0, 3, 6, 9]
>>> list(range(0, -10, -1))
[0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
>>> list(range(0))
[]
>>> list(range(1, 0))
[]
```

## 通用序列操作

大多数序列类型，包括可变类型和不可变类型都支持下表中的操作

表格中，*s* 和 *t* 是具有相同类型的序列，*n*, *i*, *j* 和 *k* 是整数而 *x* 是任何满足 *s* 所规定的类型和值限制的任意对象

`in` 和 `not in` 操作具有与比较操作相同的优先级。 `+` (拼接) 和 `*` (重复) 操作具有与对应数值运算相同的优先级

| 运算                   | 结果                                                         | 注释   |
| :--------------------- | :----------------------------------------------------------- | :----- |
| `x in s`               | 如果 *s* 中的某项等于 *x* 则结果为 `True`，否则为 `False`    | (1)    |
| `x not in s`           | 如果 *s* 中的某项等于 *x* 则结果为 `False`，否则为 `True`    | (1)    |
| `s + t`                | *s* 与 *t* 相拼接                                            | (6)(7) |
| `s * n` 或 `n * s`     | 相当于 *s* 与自身进行 *n* 次拼接                             | (2)(7) |
| `s[i]`                 | *s* 的第 *i* 项，起始为 0                                    | (3)    |
| `s[i:j]`               | *s* 从 *i* 到 *j* 的切片                                     | (3)(4) |
| `s[i:j:k]`             | *s* 从 *i* 到 *j* 步长为 *k* 的切片                          | (3)(5) |
| `len(s)`               | *s* 的长度                                                   |        |
| `min(s)`               | *s* 的最小项                                                 |        |
| `max(s)`               | *s* 的最大项                                                 |        |
| `s.index(x[, i[, j]])` | *x* 在 *s* 中首次出现项的索引号（索引号在 *i* 或其后且在 *j* 之前） | (8)    |
| `s.count(x)`           | *x* 在 *s* 中出现的总次数                                    |        |

## 可变序列操作

表格中的 *s* 是可变序列类型的实例，*t* 是任意可迭代对象，而 *x* 是符合对 *s* 所规定类型与值限制的任何对象 (例如，[`bytearray`](https://docs.python.org/zh-cn/3/library/stdtypes.html?highlight=list append#bytearray) 值限制的整数)。

| 运算                      | 结果                                                         | 注释 |
| :------------------------ | :----------------------------------------------------------- | :--- |
| `s[i] = x`                | 将 *s* 的第 *i* 项替换为 *x*                                 |      |
| `s[i:j] = t`              | 将 *s* 从 *i* 到 *j* 的切片替换为可迭代对象 *t* 的内容       |      |
| `del s[i:j]`              | 等同于 `s[i:j] = []`                                         |      |
| `s[i:j:k] = t`            | 将 `s[i:j:k]` 的元素替换为 *t* 的元素                        | (1)  |
| `del s[i:j:k]`            | 从列表中移除 `s[i:j:k]` 的元素                               |      |
| `s.append(x)`             | 将 *x* 添加到序列的末尾 (等同于 `s[len(s):len(s)] = [x]`)    |      |
| `s.clear()`               | 从 *s* 中移除所有项 (等同于 `del s[:]`)                      | (5)  |
| `s.copy()`                | 创建 *s* 的浅拷贝 (等同于 `s[:]`)                            | (5)  |
| `s.extend(t)` 或 `s += t` | 用 *t* 的内容扩展 *s* (基本上等同于 `s[len(s):len(s)] = t`)  |      |
| `s *= n`                  | 使用 *s* 的内容重复 *n* 次来对其进行更新                     | (6)  |
| `s.insert(i, x)`          | 在由 *i* 给出的索引位置将 *x* 插入 *s* (等同于 `s[i:i] = [x]`) |      |
| `s.pop([i])`              | 提取在 *i* 位置上的项，并将其从 *s* 中移除；i省略时，默认移除最后一位 | (2)  |
| `s.remove(x)`             | 删除 *s* 中第一个 `s[i]` 等于 *x* 的项目。                   | (3)  |
| `s.reverse()`             | 就地将列表中的元素逆序。                                     |      |

```python
>>> lst = list('abcde')

>>> lst.append('f')
>>> lst
['a', 'b', 'c', 'd', 'e', 'f']

>>> lst[0] = 'A'
>>> lst
['A', 'b', 'c', 'd', 'e', 'f']

>>> lst[-1] = 'F'
>>> lst
['A', 'b', 'c', 'd', 'e', 'F']

>>> del lst[0]
>>> lst
['b', 'c', 'd', 'e', 'F']

# copy 值 指针 对象 引用
>>> lst1 = [1,2,3]
>>> lst2 = lst1
>>> lst1.append(4)
>>> print(lst1,lst2)
[1, 2, 3, 4] [1, 2, 3, 4]

>>> lst1 = [1,2,3]
>>> lst2 = lst1.copy()
>>> lst1.append(4)
>>> print(lst1,lst2)
[1, 2, 3, 4] [1, 2, 3]
```

## 映射类型-字典dict

映射属于可变对象。 目前仅有一种标准映射类型 *字典*。

字典的键 *几乎* 可以是任何值。 非 [hashable](https://docs.python.org/zh-cn/3/glossary.html#term-hashable) 的值，即包含列表、字典或其他可变类型的值（此类对象基于值而非对象标识进行比较）不可用作键。 数字类型用作键时遵循数字比较的一般规则：如果两个数值相等 (例如 `1` 和 `1.0`) 则两者可以被用来索引同一字典条目。 （但是请注意，由于计算机对于浮点数存储的只是近似值，因此将其用作字典键是不明智的。）

字典可以通过将以逗号分隔的 `键: 值` 对列表包含于花括号之内来创建，例如: `{'jack': 4098, 'sjoerd': 4127}` 或 `{4098: 'jack', 4127: 'sjoerd'}`，也可以通过 [`dict`](https://docs.python.org/zh-cn/3/library/stdtypes.html#dict) 构造器来创建。

### 构建字典

```python
- class dict(**kwarg)
- class dict(mapping, **kwarg)
- class dict(iterable, **kwarg)

字典可用多种方式来创建:

- 使用花括号内以逗号分隔 键: 值 对的方式: {'jack': 4098, 'sjoerd': 4127} or {4098: 'jack', 4127: 'sjoerd'}
- 使用字典推导式: {}, {x: x ** 2 for x in range(10)}
- 使用类型构造器: dict(), dict([('foo', 100), ('bar', 200)]), dict(foo=100, bar=200)

如果没有给出位置参数，将创建一个空字典。 如果给出一个位置参数并且其属于映射对象，将创建一个具有与映射对象相同键值对的字典。 
  否则的话，位置参数必须为一个 iterable 对象。 该可迭代对象中的每一项本身必须为一个刚好包含两个元素的可迭代对象。 
  每一项中的第一个对象将成为新字典的一个键，第二个对象将成为其对应的值。 
  如果一个键出现一次以上，该键的最后一个值将成为其在新字典中对应的值。

如果给出了关键字参数，则关键字参数及其值会被加入到基于位置参数创建的字典。 
如果要加入的键已存在，来自关键字参数的值将替代来自位置参数的值。

作为演示，以下示例返回的字典均等于 {"one": 1, "two": 2, "three": 3}:

>>> a = dict(one=1, two=2, three=3)
>>> b = {'one': 1, 'two': 2, 'three': 3}
>>> c = dict(zip(['one', 'two', 'three'], [1, 2, 3]))
>>> d = dict([('two', 2), ('one', 1), ('three', 3)])
>>> e = dict({'three': 3, 'one': 1, 'two': 2})
>>> f = dict({'one': 1, 'three': 3}, two=2)
>>> a == b == c == d == e == f
True
```

### 字典操作

```python
d[key]
返回 d 中以 key 为键的项。 如果映射中不存在 key 则会引发 KeyError。

d[key] = value
将 d[key] 设为 value。

del d[key]
将 d[key] 从 d 中移除。 如果映射中不存在 key 则会引发 KeyError。

key in d
如果 d 中存在键 key 则返回 True，否则返回 False。

items()
返回由字典项 ((键, 值) 对) 组成的一个新视图
sales = {'Tony': 284, 'Mike': 280, 'John': 253, 'Peter': 213, 'Henry': 205}
for item in sales.items():
    print(item, end=' ')
# ('Tony', 284) ('Mike', 280) ('John', 253) ('Peter', 213) ('Henry', 205) 
for key, value in sales.items():
    print(key, value, end=' , ')
# Tony 284 , Mike 280 , John 253 , Peter 213 , Henry 205 , 

keys()
返回由字典键组成的一个新视图。
print(sales.keys()) # dict_keys(['Tony', 'Mike', 'John', 'Peter', 'Henry'])

values()
返回由字典值组成的一个新视图。
print(sales.values()) # dict_values([284, 280, 253, 213, 205])
两个 dict.values() 视图之间的相等性比较将总是返回 False。 这在 dict.values() 与其自身比较时也同样适用
>>> d = {'a': 1}
>>> d.values() == d.values()
False

get(key[, default])
如果 key 存在于字典中则返回 key 的值，否则返回 default。 
如果 default 未给出则默认为 None，因而此方法绝不会引发 KeyError。
print(sales['Alex']) # KeyError: 'Alex'
print(sales.get('Alex')) # None
print(sales.get('Alex',100)) # 100

# 求字典中值最大的键
>>> dic = {'a':3,'b':2,'c':1}
>>> max_key = max(dic, key=dic.get)
>>> max_key
'a'
1、max() 函数中没有 key 参数时，求的是 key 的最大值
2、max() 函数中有 key 参数时，求的是 value 的最大值
3、key 后面跟的是函数名，注意此处不是调用函数
# max(iterable, *[, key, default])
# max(arg1, arg2, *args[, key])
# 返回可迭代对象中最大的元素，或者返回两个及以上实参中最大的。
# 如果只提供了一个位置参数，它必须是非空 iterable，返回可迭代对象中最大的元素；
# 如果提供了两个及以上的位置参数，则返回最大的位置参数。
# 有两个可选只能用关键字的实参。key 实参指定排序函数用的参数，如传给 list.sort() 的。
# default 实参是当可迭代对象为空时返回的值。如果可迭代对象为空，并且没有给 default ，则会触发 ValueError。
```

## 集合类型-set

集合是具有唯一性的 hashable 对象所组成的无序多项集。 常见的用途包括成员检测、从序列中去除重复项以及数学中的集合类计算，例如交集、并集、差集与对称差集等等

目前有两种内置集合类型，`set` 和 `frozenset` 。

`set`类型是可变的 --- 其内容可以使用 `add()` 和 `remove()` 这样的方法来改变。 由于是可变类型，它没有哈希值，且不能被用作字典的键或其他集合的元素。 

`frozenset`类型是不可变并且为 hashable--- 其内容在被创建后不能再改变；因此它可以被用作字典的键或其他集合的元素。

与其他多项集一样，集合也支持 `x in set`, `len(set)` 和 `for x in set`。 作为一种无序的多项集，集合并不记录元素位置或插入顺序。 相应地，集合不支持索引、切片或其他序列类的操作。

### 构建集合

```python
class set([iterable])
class frozenset([iterable])
返回一个新的 set 或 frozenset 对象，其元素来自于 iterable。
集合的元素必须为 hashable。 要表示由集合对象构成的集合，所有的内层集合必须为 frozenset 对象。 
如果未指定 iterable，则将返回一个新的空集合。

集合可用多种方式来创建:

使用花括号内以逗号分隔元素的方式: {'jack', 'sjoerd'}
使用集合推导式: {c for c in 'abracadabra' if c not in 'abc'}
使用类型构造器: set(), set('foobar'), set(['a', 'b', 'foo'])

>>> nums = {1,2,3,4,5}
>>> nums
{1, 2, 3, 4, 5}

# 列表是可重复的，集合不可重复
>>> lst = [1,1,2,2,3,3,4,4,5,5]
>>> nums = set(lst)
>>> nums
{1, 2, 3, 4, 5}
# 元组是可重复的，集合不可重复
>>> tpl = ('a','b','c','a','b','c')
>>> nums = set(tpl)
>>> nums
{'b', 'c', 'a'}
```

### 集合操作

```python
>>> nums
{1, 2, 3, 4, 5}

>>> print(list(nums))
[1, 2, 3, 4, 5]
>>> print(tuple(nums))
(1, 2, 3, 4, 5)

>>> for n in nums:
...     print(n,end=',')
...
1,2,3,4,5,

len(s)
返回集合 s 中的元素数量（即 s 的基数）。
>>> len(nums)
5

x in s
检测 x 是否为 s 中的成员。
>>> 5 in nums
True

x not in s
检测 x 是否非 s 中的成员。
>>> 6 in nums
False
```

### set操作

```python
set是可变集合，但是只能添加元素和删除元素，不能修改元素，因为集合是无序的，没有索引
>>> nums
{1, 2, 3, 4, 5}

add(elem)
将元素 elem 添加到集合中。
>>> nums.add(6)
>>> nums
{1, 2, 3, 4, 5, 6}

remove(elem)
从集合中移除元素 elem。 如果 elem 不存在于集合中则会引发 KeyError。
>>> nums.remove(6)
>>> nums
{1, 2, 3, 4, 5}

discard(elem)
如果元素 elem 存在于集合中则将其移除。

pop()
从集合中移除并返回任意一个元素。 如果集合为空则会引发 KeyError。

clear()
从集合中移除所有元素。

intersection(*others)
set & other & ...
返回一个新集合，其中包含原集合以及 others 指定的所有集合中共有的元素。
# 交集
s1 = {1, 2, 3}
s2 = {3, 4, 5}
print(s1.intersection(s2)) # {3}

union(*others)
set | other | ...
返回一个新集合，其中包含来自原集合以及 others 指定的所有集合中的元素。
# 并集
s1 = {1, 2, 3}
s2 = {3, 4, 5}
print(s1.union(s2)) # {1, 2, 3, 4, 5}

issubset(other)
set <= other
检测是否集合中的每个元素都在 other 之中。
set < other
检测集合是否为 other 的真子集，即 set <= other and set != other。
# 子集
sub = {1, 2, 3}
sup = {1, 2, 3, 4, 5}
print(sub.issubset(sup)) # True

issuperset(other)
set >= other
检测是否 other 中的每个元素都在集合之中。
set > other
检测集合是否为 other 的真超集，即 set >= other and set != other。
# 父集
sub = {1, 2, 3}
sup = {1, 2, 3, 4, 5}
print(sup.issuperset(sub)) # True
```

# 函数

## 定义函数

```python
def 函数名([参数...]):
  """文档字符串"""
  # 函数体
```

*定义* 函数使用关键字 [`def`](https://docs.python.org/zh-cn/3/reference/compound_stmts.html#def)，后跟函数名与括号内的形参列表。函数语句从下一行开始，并且必须缩进。

函数内的第一条语句是字符串时，该字符串就是文档字符串。也称为 *docstring*，详见 [文档字符串](https://docs.python.org/zh-cn/3/tutorial/controlflow.html#tut-docstrings)。利用文档字符串可以自动生成在线文档或打印版文档，还可以让开发者在浏览代码时直接查阅文档；Python 开发者最好养成在代码中加入文档字符串的好习惯。

以下是文档字符串内容和格式的约定。

第一行应为对象用途的简短摘要。为保持简洁，不要在这里显式说明对象名或类型，因为可通过其他方式获取这些信息（除非该名称碰巧是描述函数操作的动词）。这一行应以大写字母开头，以句点结尾。

文档字符串为多行时，第二行应为空白行，在视觉上将摘要与其余描述分开。后面的行可包含若干段落，描述对象的调用约定、副作用等。

函数定义支持可变数量的参数。这里列出三种可以组合使用的形式。

- 默认值参数

  为参数指定默认值是非常有用的方式。调用函数时，可以使用比定义时更少的参数，例如：

  ```python
  def ask_ok(prompt, retries=4, reminder='Please try again!'):
      while True:
          ok = input(prompt)
          if ok in ('y', 'ye', 'yes'):
              return True
          if ok in ('n', 'no', 'nop', 'nope'):
              return False
          retries = retries - 1
          if retries < 0:
              raise ValueError('invalid user response')
          print(reminder)
  ```

  该函数可以用以下方式调用：

  - 只给出必选实参：`ask_ok('Do you really want to quit?')`
  - 给出一个可选实参：`ask_ok('OK to overwrite the file?', 2)`
  - 给出所有实参：`ask_ok('OK to overwrite the file?', 2, 'Come on, only yes or no!')`

- 关键字参数

  `kwarg=value` 形式的 [关键字参数](https://docs.python.org/zh-cn/3/glossary.html#term-keyword-argument) 也可以用于调用函数。函数示例如下：

  ```python
  def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
      print("-- This parrot wouldn't", action, end=' ')
      print("if you put", voltage, "volts through it.")
      print("-- Lovely plumage, the", type)
      print("-- It's", state, "!")
  ```

  该函数接受一个必选参数（`voltage`）和三个可选参数（`state`, `action` 和 `type`）。该函数可用下列方式调用：

  ```python
  parrot(1000)                                          # 1 positional argument
  parrot(voltage=1000)                                  # 1 keyword argument
  parrot(voltage=1000000, action='VOOOOOM')             # 2 keyword arguments
  parrot(action='VOOOOOM', voltage=1000000)             # 2 keyword arguments
  parrot('a million', 'bereft of life', 'jump')         # 3 positional arguments
  parrot('a thousand', state='pushing up the daisies')  # 1 positional, 1 keyword
  ```

  函数调用时，关键字参数必须跟在位置参数后面。

## 函数返回值

和其他语言一样，return返回

```python
def multiply(num1, num2):
  return num1 * num2
```

没有return函数返回None

```python
def no_return():
  pass

print(no_return())
# None
```

## 匿名函数-lambda表达式

`lambda`关键字用于创建小巧的匿名函数。`lambda a, b: a+b` 函数返回两个参数的和。Lambda 函数可用于任何需要函数对象的地方。在语法上，匿名函数只能是单个表达式。在语义上，它只是常规函数定义的语法糖。与嵌套函数定义一样，lambda 函数可以引用包含作用域中的变量：

```python
>>> def make_incrementor(n):
...     return lambda x: x + n
...
>>> f = make_incrementor(42)
>>> f(0)
42
>>> f(1)
43
```

上例用 lambda 表达式返回函数。还可以把匿名函数用作传递的实参：

```python
>>> pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
>>> pairs.sort(key=lambda pair: pair[1])
>>> pairs
[(4, 'four'), (1, 'one'), (3, 'three'), (2, 'two')]
```

# 面向对象

## 类对象

### 定义类

定义类时，类名首字母习惯大写。

```python
最简单的类定义看起来像这样:
class ClassName:
    <statement-1>
    .
    .
    .
    <statement-N>
    
class Dog:
  pass
```

### 类属性和方法

定义类的属性和方法：

```python
# 类属性
class Goods:
    count = 0 # 类属性

    def __init__(self):
        Goods.count += 1
        self.name = '' # 对象属性
        self.price = 0
        self.discount = 1

print(Goods.count)
# 0
# 类属性不需要创建对象，直接通过类名.属性名就可访问

goods1 = Goods()
goods2 = Goods()
goods3 = Goods()

print(Goods.count)
# 3

# 类方法
class Goods:
    id_count = 0

    @classmethod # @classmethod装饰器，用来定义类方法
    def generate_id(cls): # Goods类定义了一个generate_id()类方法，参数cls是类方法固定的，代表类自己
        cls.id_count += 1
        return cls.id_count

    def __init__(self):
        self.id = self.generate_id()
        self.name = ''
        self.price = 0
        self.discount = 1

goods1 = Goods()
print(goods1.id) # 1
goods2 = Goods()
print(goods2.id) # 2
goods3 = Goods()
print(goods3.id) # 3
```

类对象支持两种操作：属性引用和实例化。

*属性引用* 使用 Python 中所有属性引用所使用的标准语法: `obj.name`。有效的属性名称是类对象被创建时存在于类命名空间中的所有名称。 因此，如果类定义是这样的:

```python
class MyClass:
    """A simple example class"""
    i = 12345

    def f(self):
        return 'hello world'
```

那么 `MyClass.i` 和 `MyClass.f` 就是有效的属性引用，将分别返回一个整数和一个函数对象。 类属性也可以被赋值，因此可以通过赋值来更改 `MyClass.i` 的值。 `__doc__` 也是一个有效的属性，将返回所属类的文档字符串: `"A simple example class"`。

类的 *实例化* 使用函数表示法。 可以把类对象视为是返回该类的一个新实例的不带参数的函数。 举例来说（假设使用上述的类）:

```python
x = MyClass()
```

创建类的新 *实例* 并将此对象分配给局部变量 `x`。

## 实例对象

实例对象所能理解的唯一操作是属性引用。 有两种有效的属性名称：数据属性和方法。

### 检测对象

```python
# type()
class type(object)
class type(name, bases, dict, **kwds)

传入一个参数时，返回 object 的类型。 返回值是一个 type 对象，通常与 object.__class__所返回的对象相同。

>>> type(1)
<class 'int'>
>>> type('a')
<class 'str'>

>>> class Dog:
...     pass
>>> dog = Dog()
>>> type(dog)
<class '__main__.Dog'>

# isinstance()
推荐使用 isinstance()内置函数来检测对象的类型，因为它会考虑子类的情况。

>>> isinstance(dog,Dog)
True
>>> isinstance(1,int)
True
>>> isinstance([],list)
True

# dir()
dir([object])
如果没有实参，则返回当前本地作用域中的名称列表。如果有实参，它会尝试返回该对象的有效属性列表。

# is 判断是否是同一个对象
>>> lst1 = []
>>> lst2 = []
>>> lst3 = lst1

>>> lst1 == lst2
True
>>> lst1 is lst2
False
>>> lst1 is lst3
True

# id() 返回对象在内存中的地址
>>> id(lst1)
4422382208
>>> id(lst2)
4422387456
>>> id(lst3)
4422382208
```

### 对象属性和方法

```python
class Dog:
    def __init__(self):
        self.breed = None
        self.color = None
        self.size = None

    def eat(self):
        print('I like bones')

    def run(self):
        print('I will catch you')

    def bark(self):
        print('Wang! Wang!')

dog = Dog()
dog.eat()
dog.run()
dog.bark()
dog.breed = '哈士奇'
dog.color = '黑白'
dog.size = '大'
print("一直%s型%s色的%s" % (dog.size, dog.color, dog.breed))

每次在对象创建完再设置属性就很麻烦，
这时候就可以使用__init__(self)函数来接收参数，把属性的值作为参数在初始化对象的时候就传给它
__init__(self)函数是python用来初始化对象的构造函数，或者说也叫初始化函数。
object.__init__(self[, ...])
在实例 (通过 __new__()) 被创建之后，返回调用者之前调用。其参数与传递给类构造器表达式的参数相同。
因为对象是由 __new__() 和 __init__() 协作构造完成的 (由 __new__() 创建，并由 __init__() 定制)，
所以 __init__() 返回的值只能是 None，否则会在运行时引发 TypeError。

class Dog:
    def __init__(self, breed, color, size):
        self.breed = breed
        self.color = color
        self.size = size

    def eat(self):
        print('I like bones')

    def run(self):
        print('I will catch you')

    def bark(self):
        print('Wang! Wang!')

dog = Dog('哈士奇', '黑白', '大')
dog.eat()
dog.run()
dog.bark()
print("一直%s型%s色的%s" % (dog.size, dog.color, dog.breed))
```

# 模块

## 导入模块

```python
import 模块名

# 导入整个模块
import math
math.log(10)

# 导入多个模块
import math,os

# 导入指定的函数
from math import log
log(10)
```

## 自定义模块

```python
# 都在同级目录下
文件路径
/--
  |-a.py
  |-b.py
  |-main.py
  
main中导入a和b
# / main.py
import a,b
或者
from a import 函数、变量、类
from b import 函数、变量、类

# 模块文件不在同级目录
# 在项目目录下，新建一个Python Package模块目录，命名为pyPkg，用来存放a.py,b.py两个模块文件
文件路径
/--
  |-pyPkg
  	|--a.py
    |--b.py
  |-main.py
  
main中导入a和b
# / main.py
import pyPkg.a,pyPkg.b
或者
from pyPkg.a import 函数、变量、类
from pyPkg.b import 函数、变量、类
```

模块不需要导出语句，只需要导入的时候使用import语句，写明导入模块的路径，以及要导入的模块，如果使用from的方式，写明要导入该模块中的哪个函数、变量、类

python中模块的路径不需要写`/.../...`，如果模块和使用模块的文件在同一级目录，直接写模块文件名即可，如果同级目录的字目录下，通过`.`的方式写层级关系。

## \_\_name\_\_

模块是包含 Python 定义和语句的文件。其文件名是模块名加后缀名 `.py` 。在模块内部，通过全局变量 `__name__` 可以获取模块名（即字符串）。

```python
# /pyPkg/a.py
print(__name__)

pyPkg.a

# a.py是被运行文件
print(__name__)

__main__
```

当文件不是直接被运行的文件，\_\_name\_\_返回的是模块名。

当文件是直接被运行的文件时，\_\_name\_\_返回的是\_\_main\_\_。

### 以脚本方式执行模块

可以用以下方式运行 Python 模块：

```python
python fibo.py <arguments>
```

这项操作将执行模块里的代码，和导入模块一样，但会把 `__name__` 赋值为 `"__main__"`。 也就是把下列代码添加到模块末尾：

```python
if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```

既可以把这个文件当脚本使用，也可以用作导入的模块， 因为，解析命令行的代码只有在模块以 “main” 文件执行时才会运行：

```python
$ python fibo.py 50
0 1 1 2 3 5 8 13 21 34
```

导入模块时，不运行这些代码：

```python
>>> import fibo
>>>
```

这种操作常用于为模块提供便捷用户接口，或用于测试（把模块当作执行测试套件的脚本运行）。

## 日期时间模块

```python
from datetime import datetime, timedelta
import time

# 返回当前时间的datetime对象
now = datetime.now()
print(now)					2021-06-15 23:15:52.560464
print(type(now))		<class 'datetime.datetime'>

# 时间戳 1970-01-01 到 当前时间的秒数
print(now.timestamp()) 	1623770152.560464
print(time.time()) 			1623770152.560698

# 时间转成字符串
print(now.strftime('%Y-%m-%d %H:%M:%S'))		2021-06-15 23:15:52
print(time.strftime('%Y-%m-%d %H:%M:%S'))		2021-06-15 23:15:52

# strptime 将字符串解析为给定相应格式的 datetime 对象

    
# 年，月，日
print(now.year, now.month, now.day)			2021 6 15

# 返回去年的当前时间
print(now.replace(year=2020))						2020-06-15 23:15:52.560464
    
# 计算两个日期相隔多少天
delta = now - now.replace(year=2020)		
print(delta)														365 days, 0:00:00
print(type(delta))											<class 'datetime.timedelta'>
print(delta.days, delta.seconds)				365 0
    
# 计算某个日期加上多少天后的日期
print(now + delta)											2022-06-15 23:15:52.560464
    
# 当前时间+20天后的时间
print(now + timedelta(days=20))					2021-07-05 23:15:52.560464
    
# 当前时间-2个半小时前的时间
print(now - timedelta(hours=2, minutes=30))			2021-06-15 20:45:52.560464
# 定时器，延迟3秒后执行
print('开始延迟')									开始延迟
time.sleep(3)											3s后
print('延迟结束')									延迟结束
```

## 随机数模块

```python
import random

# random.random 随机生成1个0-1之间的浮点数
print(random.random())
0.07390137939832786

# random.randint 随机生成1个1-100之间的整数，包括1和100
print(random.randint(1, 100))
5

# random.choice 从一个序列中随机抽取出1个元素，序列不能为空
az = [chr(i) for i in range(65, 65 + 26)]
print(az)
['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z']
print(random.choice(az))
F

# random.choices 随机抽取多个 会重复
print(random.choices(az, k=2))
['D', 'P']
print(random.choices(az, k=4))
['G', 'M', 'M', 'M']

# random.sample 随机抽取多个 不重复
print(random.sample(az, k=6))
['L', 'K', 'V', 'Y', 'R', 'N']

# random.shuffle 将可变的迭代序列原地随机打乱
random.shuffle(az)
print(az)
['H', 'U', 'E', 'B', 'D', 'N', 'X', 'S', 'L', 'V', 'M', 'Q', 'C', 'T', 'W', 'Y', 'K', 'R', 'F', 'G', 'O', 'I', 'Z', 'J', 'P', 'A']
```

## OS模块

```python
# os_.py
import os

# 获取当前工作目录
print(os.getcwd())
/Users/ethan/PycharmProjects/pythonProject

# os.mkdir 创建目录
os.mkdir(os.getcwd() + '/test')
/Users/ethan/PycharmProjects/pythonProject下新建了一个test目录

# os.makedirs 创建多层目录
os.makedirs(os.getcwd() + '/abc/123')
/Users/ethan/PycharmProjects/pythonProject下创建了/abc/123

# os.listdir 列出指定目录下的全部目录
print(os.listdir(os.getcwd()))
['abc', 'os_.py', 'test', 'a.py', '__pycache__', 'pyPkg', 'b.py', 'helloWorld.py', 'venv', 'ifesle.py', '.idea']

# os.path模块
# os.path.abspath 显示当前目录的绝对路径
print(os.path.abspath('./'))
/Users/ethan/PycharmProjects/pythonProject

# os.path.abspath 显示当前文件的绝对路径
print(os.path.abspath(__file__))
/Users/ethan/PycharmProjects/pythonProject/os_.py
# __file__ python内置变量，表示当前代码文件的路径
print(__file__)
/Users/ethan/PycharmProjects/pythonProject/os_.py

# os.path.isfile 判断指定路径文件是否存在，返回True/False，只有文件返回True，目录返回False
print(os.path.isfile(os.path.abspath(__file__)))
True
print(os.path.isfile(os.path.abspath('./')))
False

# os.sep 返回当前系统下的目录分隔符，windows \ Mac&linux /
print(os.sep)
/

# os.path.join 拼接指定路径
print(os.path.join(os.getcwd(), 'abc', '123'))
/Users/ethan/PycharmProjects/pythonProject/abc/123

# os.path.dirname 返回路径的目录名称。
print(os.path.dirname(__file__))
/Users/ethan/PycharmProjects/pythonProject
# os.path.basename 返回指定路径的基本名称
print(os.path.basename(__file__))
os_.py

# os.walk(top, topdown=True, onerror=None, followlinks=False)
# 生成目录树中的文件名，方式是按上->下或下->上顺序浏览目录树。
# 对于以 top 为根的目录树中的每个目录（包括 top 本身），它都会生成一个三元组 (dirpath, dirnames, filenames)。
# dirpath 是表示目录路径的字符串。 
# dirnames 是 dirpath 中子目录名称组成的列表 (excluding '.' and '..')。 
# filenames 是 dirpath 中非目录文件名称组成的列表。 
```

## SYS模块

```python
# sys_.py
import sys

# sys.path 一个由字符串组成的列表，用于指定模块的搜索路径。
for i in sys.path:
    print(i)
/Users/ethan/PycharmProjects/pythonProject
/Users/ethan/PycharmProjects/pythonProject
/Applications/PyCharm.app/Contents/plugins/python/helpers/pycharm_display
/usr/local/Cellar/python@3.9/3.9.2_4/Frameworks/Python.framework/Versions/3.9/lib/python39.zip
/usr/local/Cellar/python@3.9/3.9.2_4/Frameworks/Python.framework/Versions/3.9/lib/python3.9
/usr/local/Cellar/python@3.9/3.9.2_4/Frameworks/Python.framework/Versions/3.9/lib/python3.9/lib-dynload
/Users/ethan/PycharmProjects/pythonProject/venv/lib/python3.9/site-packages
/Applications/PyCharm.app/Contents/plugins/python/helpers/pycharm_matplotlib_backend

# sys.argv 一个列表，其中包含了被传递给 Python 脚本的命令行参数。argv[0] 为脚本的名称
print(sys.argv)
$ python sys_.py
['sys_.py']
$ python sys_.py 123
['sys_.py', '123']
$ python sys_.py 123 456
['sys_.py', '123', '456']
```





