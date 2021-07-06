---
title: 【数据分析】第五阶段：python（三）Numpy科学计算库
date: 2021-06-24 00:05:20
tags:
- 数据分析
- python
- Numpy
categories:
- 学习笔记
description: Numpy实现科学计算，通过Numpy生成ndarray、索引和切片，对数组进行面向数组编程。
---

# Numpy基础

## Numpy介绍和安装

NumPy(Numerical Python)是Python的一种开源的数值计算扩展。提供多维数组对象，各种派生对 象(如掩码数组和矩阵)，这种工具可用来存储和处理大型矩阵，比Python自身的嵌套列表(nested list structure)结构要高效的多(该结构也可以用来表示矩阵(matrix))，支持大量的维度数组与矩阵 运算，此外也针对数组运算提供大量的数学函数库，包括数学、逻辑、形状操作、排序、选择、输入输出、离散傅立叶变换、基本线性代数，基本统计运算和随机模拟等等。

安装：

```bash
pip install jupyter
pip install numpy
```

jupyter是一个开发工具

打开 jupyter：

```bash
jupyter notebook
```

## jupyter操作、numpy创建数组

NumPy的数组类称为ndarray，也被称为别名 array。请注意，numpy.array这与标准Python库类不同 array.array，后者仅处理一维数组且功能较少。

```python
import numpy as np # Shift + Enter 执行

# 创建可以将Python中的list，转换成NumPy数组
l = [1,2,3,4,5]
nd1 = np.array(l) # arr + Tab 补全提示
print(nd1)
display(nd1) # 显示
# [1 2 3 4 5]
	array([1, 2, 3, 4, 5])

nd2 = np.zeros(shape = (3,4), dtype = np.int16) # shift + Tab 提示方法的属性，使用
nd2
# array([[0, 0, 0, 0],
       [0, 0, 0, 0],
       [0, 0, 0, 0]], dtype=int16)

nd3 = np.ones(shape = (3,5), dtype = np.float32)
nd3 # jupyter中执行程序，代码，最后一行，默认就是输出
# array([[1., 1., 1., 1., 1.],
       [1., 1., 1., 1., 1.],
       [1., 1., 1., 1., 1.]], dtype=float32)

# 三位数组
nd4 = np.full(shape = (3,4,5), fill_value = 3.1415926) # 生成任意指定的数组
nd4
# array([[[3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926]],

       [[3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926]],

       [[3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926],
        [3.1415926, 3.1415926, 3.1415926, 3.1415926, 3.1415926]]])

nd5 = np.random.randint(0,100,size=20) # 从0到100，生成20个随机数，int整数
nd5
# array([38, 30, 86, 41, 85, 30, 15, 75, 43, 71,  1, 56, 57, 68, 17, 27, 17,
       83, 40, 72])

nd6 = np.random.rand(3,5) # 生成0～1之间的随机数
nd6
# array([[0.77663803, 0.11702091, 0.18031498, 0.47348888, 0.05004264],
       [0.84740708, 0.11633668, 0.71007798, 0.03210735, 0.48758925],
       [0.64457027, 0.61373153, 0.82304435, 0.24623281, 0.31574082]])

nd7 = np.random.randn(3,5) # 正态分布，平均值是0，标准差是1
display(nd7)
# array([[-2.45795376,  0.10289315,  0.42696645, -1.20559391, -0.06939776],
       [-0.53512744, -0.34935876, -0.8623595 , -0.37741174,  0.58421729],
       [-1.32914239, -0.79446503, -0.01809647, -0.12363101, -0.45921063]])

nd8 = np.random.normal(loc=175, scale=10, size=(3,5)) # 正态分布，平均值是175，标准差是10
print(nd8)
# [[169.58727647 167.24390077 178.3448052  189.29646055 168.7064162 ]
 [176.66889153 174.24171292 164.55265256 182.73991813 173.16207861]
 [165.72103767 189.17317896 170.71889571 168.98354867 176.47549241]]

nd9 = np.arange(1,100,step=1) # 等差数列，左闭右开，100取不到
nd9
# array([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16, 17,
       18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34,
       35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51,
       52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68,
       69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85,
       86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99])

nd10 = np.linspace(1,100,num=20) # 等差数列，左闭右闭，num表示生成等差数列的长度
nd10
# array([  1.        ,   6.21052632,  11.42105263,  16.63157895,
        21.84210526,  27.05263158,  32.26315789,  37.47368421,
        42.68421053,  47.89473684,  53.10526316,  58.31578947,
        63.52631579,  68.73684211,  73.94736842,  79.15789474,
        84.36842105,  89.57894737,  94.78947368, 100.        ])
```

## jupyter插件、numpy数组属性

jupyter扩展插件：

- pip install jupyter_contrib_nbextensions
- pip install jupyter_nbextensions_configurator

配置命令：

 jupyter contrib nbextension install --user
 jupyter nbextensions_configurator enable --user

配置前关闭jupyter，配置后重启jupyter

勾选一些插件：

- Collapsible Headings：折叠标题
- Table of Contents (2)：目录大纲
- Codefolding：折叠代码
- Hinterland：输入代码时自动补全提示

```python
import numpy as np

nd = np.random.randn(5,3)
nd
# array([[ 0.06473005, -0.71088673,  1.89772894],
       [-0.11956733, -1.42285519, -0.55382711],
       [ 1.01566014,  0.34695295, -1.35793019],
       [ 0.69863095,  0.48830044, -1.1479501 ],
       [-1.32697378,  0.09521401,  1.73749091]])

nd.shape # 数组形状
# (5, 3)

nd.dtype # 数组的数据类型，float64位，一位占一个0或1
# dtype('float64')

nd.size # 数组的元素个数，数组可以是多维的，请问，里面有多少数据，5*3=15
# 15

nd.ndim # 数组维度
# 2

nd.itemsize # 元素的大小，8字节
# 数据类型是float64位，1字节=8位，8字节=64位
# 8
```

## 文件读写操作

```python
nd1 = np.random.randint(0,100,size=(3,5))
nd2 = np.random.randn(3,5)
display(nd1,nd2)
# array([[45, 34,  8, 95,  5],
       [16, 53, 50, 96, 27],
       [19, 64, 34, 35, 52]])
# array([[-2.18469582, -0.34279244, -1.45080225, -0.28285667,  1.10508641],
       [ 2.07537525, -0.24318128,  1.50747735, -1.56132013, -1.198134  ],
       [-0.61875823, -1.47236696,  0.53825821, -0.93665319, -1.15234581]])

np.save('./data',nd1) # 把一个数据存到文件中
np.load('./data.npy') # save的时候默认添加.npy后缀
# array([[45, 34,  8, 95,  5],
       [16, 53, 50, 96, 27],
       [19, 64, 34, 35, 52]])

# 多个数据存到一个文件中
np.savez('./data.npz', a = nd1, abc = nd2) # 保存数据时起名：a,abc为key，自己随意命名
data = np.load('./data.npz')
data
#  <numpy.lib.npyio.NpzFile at 0x119ec1730>
data['a'] # 单引号
# array([[45, 34,  8, 95,  5],
       [16, 53, 50, 96, 27],
       [19, 64, 34, 35, 52]])
data['abc']
# array([[-2.18469582, -0.34279244, -1.45080225, -0.28285667,  1.10508641],
       [ 2.07537525, -0.24318128,  1.50747735, -1.56132013, -1.198134  ],
       [-0.61875823, -1.47236696,  0.53825821, -0.93665319, -1.15234581]])

# 数据存储为压缩文件
np.savez_compressed('./data2.npz',x = nd1, y = nd2)
np.load('./data2.npz')['x']
# array([[45, 34,  8, 95,  5],
       [16, 53, 50, 96, 27],
       [19, 64, 34, 35, 52]])

# 数据存储为文本文件
np.savetxt(
    fname='./data.txt', # 文件名
    X=nd1, # 数据
    fmt='%0.2f', # 格式
    delimiter=',' # 分隔符
)
;
np.savetxt(
    fname='./data.csv', # 文件名
    X=nd1, # 数据
    fmt='%d', # 格式
    delimiter=';' # 分隔符
)
np.loadtxt('./data.csv',delimiter=';')
# array([[45., 34.,  8., 95.,  5.],
       [16., 53., 50., 96., 27.],
       [19., 64., 34., 35., 52.]])
np.loadtxt('./data.txt',delimiter=',')
# array([[45., 34.,  8., 95.,  5.],
       [16., 53., 50., 96., 27.],
       [19., 64., 34., 35., 52.]])
```

## numpy数据类型

ndarray的数据类型

- int: int8、uint8、int16、int32、int64 
- float: float16、float32、float64
- str

```python
# int8,int16,int32,int64,uint8无符号
# float16,float32,float64
# str字符串类型
# int8 表示 2**8个数字 256 -128 ~ 127 有符号
# uint8 表示256个数字，无符号，表明只有整数：0 ~ 255

np.array([2,4,7], dtype=np.int8) # 声明时指定数据类型
# array([2, 4, 7], dtype=int8)

# 超过范围会自动减，-3-256=253，-7-256=249，256-256=0，257-256=1
np.array([-3,-7,255,108,0,256,257], dtype=np.uint8)
# array([253, 249, 255, 108,   0,   0,   1], dtype=uint8)

np.random.randint(0,100,size = 10, dtype='int32') # 数据类型也可以用字符串'int32' 和np.int32一样
# array([80, 12, 16, 40,  9, 92,  3, 50, 98, 72], dtype=int32)

nd = np.random.rand(10,2)
nd
# array([[0.06317456, 0.7004027 ],
       [0.68729213, 0.26120042],
       [0.3896194 , 0.78455975],
       [0.05996179, 0.12533533],
       [0.82546158, 0.26727256],
       [0.06133464, 0.327031  ],
       [0.82389777, 0.37362349],
       [0.23175058, 0.02009914],
       [0.80245219, 0.46818195],
       [0.59095296, 0.6970078 ]])
nd.dtype
# dtype('float64')
np.asarray(nd,dtype='float32') # 转换数据类型
# array([[0.06317456, 0.7004027 ],
       [0.68729216, 0.26120043],
       [0.3896194 , 0.7845597 ],
       [0.05996179, 0.12533534],
       [0.82546157, 0.26727256],
       [0.06133464, 0.327031  ],
       [0.8238978 , 0.3736235 ],
       [0.23175058, 0.02009914],
       [0.8024522 , 0.46818194],
       [0.59095293, 0.6970078 ]], dtype=float32)
nd.astype(dtype='float16') # 转换数据类型
# array([[0.0632 , 0.7    ],
       [0.6875 , 0.2612 ],
       [0.3896 , 0.7847 ],
       [0.05997, 0.1254 ],
       [0.8257 , 0.2673 ],
       [0.06134, 0.3271 ],
       [0.8237 , 0.3735 ],
       [0.2318 , 0.0201 ],
       [0.8022 , 0.4683 ],
       [0.591  , 0.697  ]], dtype=float16)

# 不同数据类型对文件大小的影响
nd = np.random.randn(1000,3) # 默认数据类型float64
np.save('./data1', nd) # 文件大小：24KB
np.save('./data2', nd.astype('float16')) # 文件大小：6KB

nd = np.array(list('abcdefghi')) # 字符串类型
nd
# array(['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i'], dtype='<U1')
nd.dtype
# dtype('<U1')
```

# Numpy数组运算

## 基本运算

```python
# 加减乘除指数幂运算

nd1 = np.random.randint(0,10,size=5)
nd2 = np.random.randint(0,10,size=5)
display(nd1,nd2)
# array([8, 9, 6, 9, 3])
# array([3, 8, 3, 8, 2])

nd1 + nd2 # 返回一个新的数组对象，原来的数组，内容不变
# array([11, 17,  9, 17,  5])

nd1 - nd2
# array([5, 1, 3, 1, 1])

nd1 * nd2
# array([24, 72, 18, 72,  6])

nd1 / nd2
# array([2.66666667, 1.125     , 2.        , 1.125     , 1.5       ])

nd1 ** nd2 # 幂运算
# array([     512, 43046721,      216, 43046721,        9])

np.power(2,3) # 表示2的三次幂
# 8

np.power(nd1,nd2) # 表示nd1的nd2次幂
# array([     512, 43046721,      216, 43046721,        9])

np.log(100) # 对数运算，底数是自然底数e 2.718
# 4.605170185988092
np.log10(1000)
# 3.0
```

## 逻辑运算

```python
display(nd1,nd2)
# array([0, 3, 9, 9, 4])
# array([5, 4, 3, 9, 5])

nd1 > nd2
# array([False, False, False, False, False])

nd1 < nd2
# array([ True,  True,  True,  True,  True])

nd1 >= nd2
# array([False, False, False, False, False])

nd1 == nd2
# array([False, False, False, False, False])

(nd1>2) & (nd1<9) # & 逻辑与
# array([False,  True,  True,  True, False])

(nd1>2) | (nd1<9) # | 逻辑或
# array([ True,  True,  True,  True,  True])

~ (nd1>5) # ~ 逻辑非
# array([ True,  True,  True, False, False])
```

## 数组与标量的运算

```python
nd1
# array([8, 9, 6, 9, 3])

# 数字3，4，5...都是标量
nd1 + 10 # 所有的位置都加了10，广播
# array([18, 19, 16, 19, 13])

nd1 - 1024
# array([-1016, -1015, -1018, -1015, -1021])

nd1 * 256
# array([2048, 2304, 1536, 2304,  768])

nd1 / 1024
# array([0.0078125 , 0.00878906, 0.00585938, 0.00878906, 0.00292969])

# 数组也可以做分母，注意不能有0
1 / nd1
# array([0.125     , 0.11111111, 0.16666667, 0.11111111, 0.33333333])

1/np.array([1,3,0,5]) # 0位置，inf，
# <ipython-input-63-c14e1d8d9b62>:1: RuntimeWarning: divide by zero encountered in true_divide
  # 1/np.array([1,3,0,5]) # 0位置，inf，
# array([1.        , 0.33333333,        inf, 0.2       ])
```

## -= += *= 直接改变原数组

```python
display(nd1,nd2)
# array([8, 9, 6, 9, 3])
# array([3, 8, 3, 8, 2])

nd1 -= 100 # 没有打印输出，说明改变了原来的数组
nd1
# array([-92, -91, -94, -91, -97])

nd2 *= 3
nd2
# array([ 9, 24,  9, 24,  6])

# nd1 /= 10 数组不支持 /=
```

# Numpy拷贝

## 完全没有复制

```python
a = np.random.randint(0,10,size = 5)
b = a
display(a,b)
# array([8, 1, 8, 6, 1])
# array([8, 1, 8, 6, 1])

a is b # 返回True，说明a和b是一回事
# True

a[0] = 1024 # a,b都指向内存中同一块堆地址
display(a,b)
# array([1024,    1,    8,    6,    1])
# array([1024,    1,    8,    6,    1])
```

## 浅拷贝（也叫视图或查看）

```python
a = np.random.randint(0,10, size=5)
b = a.view() # 视图，查看，浅拷贝
display(a,b)
# array([2, 9, 2, 4, 7])
# array([2, 9, 2, 4, 7])

a is b # 说明a和b不一样
# False

a.flags.owndata # a数组数据是自己的
# True
b.flags.owndata # b是浅拷贝a的数据，也就是b并不拥有自己的数据
# False

a[0] = 1024
b[1] = 2048 # 无论修改谁，最终结果两个数组都发生改变
display(a,b)
# array([1024, 2048,    2,    4,    7])
# array([1024, 2048,    2,    4,    7])
```

## 深拷贝

```python
a = np.random.randint(0,10,size=5)
b = a.copy() # 深拷贝，此时a和b没有关系
display(a,b)
# array([5, 2, 1, 6, 9])
# array([5, 2, 1, 6, 9])

display(a is b)
display(a.flags.owndata)
display(b.flags.owndata) # b拥有自己的数据
# False
# True
# True

a[0] = 1024
b[1] = 2048
display(a,b)
# array([1024,    2,    1,    6,    9])
# array([   5, 2048,    1,    6,    9])
```

- copy应该在不再需要原来的数组情况下，切片后调用。例如，假设a是一个巨大的中间结果，而最 终结果b仅包含的一小部分a，则在b使用切片进行构造时应制作一个深拷⻉:

```python
import numpy as np
a = np.arange(1e8)
b = a[::1000000].copy() # 每100万个数据中取一个数据 
del a # 不在需要a，删除占大内存的a
b.shape # shape(100,)
```

# Numpy索引，切片

## 基本索引和切片

```python
a = np.random.randint(0,30,size=10)
a
# array([17,  0, 10, 29, 14, 29, 20,  3,  0, 11])

a[2] # 取一个
# 10

a[[0,1,2]] # 取多个
# array([17,  0, 10])

a[0:3] # 左闭右开
# array([17,  0, 10])

a[:3] # 如果冒号前面不写，默认从0开始
# array([17,  0, 10])

a[5:9] # 从某个索引开始切片
# array([29, 20,  3,  0])

a[5:] # 冒号后面不写，默认结尾结束
# array([29, 20,  3,  0, 11])

a[::2] # 2个当中取1个
# array([17, 10, 14, 20,  0])

a[3::3] # 从索引3开始，每3个取1个
# array([29, 20, 11])

a[::-1] # 从后往前倒着取，数组进行颠倒
# array([11,  0,  3, 20, 29, 14, 29, 10,  0, 17])

a[::-2] # 颠倒，2个中取1个
# array([11,  3, 29, 29,  0])

a[2::-3] # 从索引2开始，从后往前倒着取，10，0，17，每3个取1个，10
# array([10])

b = np.random.randint(0,30,size=(10,10))
b # 二维数组，多维数组索引和切片的规律与一维数组一样
# array([[ 8, 28, 17, 14, 17,  2, 19, 24,  6, 24],
       [ 6,  2,  4,  5, 26, 25, 29,  6, 16, 12],
       [ 1, 19,  8,  2, 14, 10, 28, 15, 12, 29],
       [ 2, 18, 14,  1, 18, 19, 18,  4, 25, 18],
       [12,  1, 18, 19, 25, 13,  3, 20, 24, 28],
       [20, 20,  3, 26, 20,  0,  4,  4, 22, 21],
       [11, 12, 23, 26, 23, 22, 11,  0, 10, 16],
       [26,  7,  2, 14, 19,  6, 23,  1, 18, 15],
       [22, 19,  0,  3, 15,  2, 20,  9, 10, 21],
       [24,  1, 25, 21, 21,  6,  8, 26,  3, 15]])

b[1] # 第2行数据
# array([ 6,  2,  4,  5, 26, 25, 29,  6, 16, 12])

b[[0,3,5]] # 第1，4，6行数据
# array([[ 8, 28, 17, 14, 17,  2, 19, 24,  6, 24],
       [ 2, 18, 14,  1, 18, 19, 18,  4, 25, 18],
       [20, 20,  3, 26, 20,  0,  4,  4, 22, 21]])

b[1,1] # 第2行2列数据
# 2

b[1,[1,3,5]] # 第2行2,4,6列数据
# array([ 2,  5, 25])

b[2:7,1::3] # 行：3～6，列：第2列开始到结尾，每3个取一个
# array([[19, 14, 15],
       [18, 18,  4],
       [ 1, 25, 20],
       [20, 20,  4],
       [12, 23,  0]])

b[-1,-1] # 最后一行，最后一列
15
```

## 花式索引和索引技巧

```python
a = np.arange(20)
a
# array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16,
       17, 18, 19])
  
b = a[3:7] # 切片时，返回的数据，不是深拷贝
b
# array([3, 4, 5, 6])
b[0] = 1024
display(a,b)
#array([   0,    1,    2, 1024,    4,    5,    6,    7,    8,    9,   10,
         11,   12,   13,   14,   15,   16,   17,   18,   19])
#array([1024,    4,    5,    6])

a = np.arange(20)
# 花式索引，返回的是，深拷贝的数据
b = a[[3,4,5,6]] # 花式索引：就是在索引时，给了一个数组
display(a,b)
# array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16,
       17, 18, 19])
# array([3, 4, 5, 6])
b[0] = 1024
display(a,b)
# array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14, 15, 16,
       17, 18, 19])
# array([1024,    4,    5,    6])

a = np.random.randint(0,151,size=(10,3)) # 10名学生，参加3门考试：python，math，english
a
# array([[136, 103, 144],
       [127,  37,  10],
       [ 57,  11,  53],
       [ 96, 142,  10],
       [103,  10,   7],
       [  4,  17,  59],
       [ 24,  67,  89],
       [ 85,  81,  68],
       [ 74,  22, 140],
       [ 25, 121,  41]])
      
# 选出大于等于100分的数据
a >= 100 # 逻辑运算，每一行每一列都进行逻辑判断
# array([[ True,  True,  True],
       [ True, False, False],
       [False, False, False],
       [False,  True, False],
       [ True, False, False],
       [False, False, False],
       [False, False, False],
       [False, False, False],
       [False, False,  True],
       [False,  True, False]])
cond = a >= 100
# 根据条件，筛选数据，只要大于等于100分，就返回该数据
a[cond]
# array([136, 103, 144, 127, 142, 103, 140, 121])

# 现在想选出，三门考试成绩都大于等于100分的成绩数据
# boolean True=1;False=0
# 三门科目的条件相乘，有一门是False结果就是0，三门都是True结果是1
cond2 = cond[:,0]*cond[:,1]*cond[:,2] # 数组的*运算：每行第1列*每行第2列*每行第3列
cond2
# array([ True, False, False, False, False, False, False, False, False,
       False])
a[cond2]
# array([[136, 103, 144]])

# 选出三门考试平均成绩大于90的学生成绩数据
# mean()方法求平均值，axis参数：
# 不设置，求m*n个数的均值，返回一个实数
# axis=0，对各列求均值，返回1*n矩阵
# axis=1，对各行求均值，返回m*1矩阵
a.mean(axis=1) 
# array([127.66666667,  58.        ,  40.33333333,  82.66666667,
        40.        ,  26.66666667,  60.        ,  78.        ,
        78.66666667,  62.33333333])

a.mean(axis=1) > 90
# array([ True, False, False, False, False, False, False, False, False,
       False])

cond3 = a.mean(axis=1) > 90
a[cond3]
# array([[136, 103, 144]])
```

# Numpy形状操作

## 数组变形

```python
a = np.random.randint(0,10,size=(3,5))
a
# array([[9, 3, 3, 7, 2],
       [4, 9, 2, 8, 2],
       [0, 7, 9, 6, 3]])
    
a.reshape(5,3)
# array([[9, 3, 3],
       [7, 2, 4],
       [9, 2, 8],
       [2, 0, 7],
       [9, 6, 3]])

a.reshape(1,15)
# array([[9, 3, 3, 7, 2, 4, 9, 2, 8, 2, 0, 7, 9, 6, 3]])

a.reshape(2,15) # 变形的size必需和原来一致
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-148-cfa7d02313ac> in <module>
----> 1 a.reshape(2,15)

ValueError: cannot reshape array of size 15 into shape (2,15)

  
a.reshape(1,1,15) # 还可以转成三维数组
# array([[[9, 3, 3, 7, 2, 4, 9, 2, 8, 2, 0, 7, 9, 6, 3]]])

a.reshape(-1,3) # 自动整形：-1表示数据，3自动计算-1 = 5
# array([[9, 3, 3],
       [7, 2, 4],
       [9, 2, 8],
       [2, 0, 7],
       [9, 6, 3]])
```

## 数组转置

```python
a
# array([[9, 3, 3, 7, 2],
       [4, 9, 2, 8, 2],
       [0, 7, 9, 6, 3]])
    
# 转置，行变列，列变行
a.T # 3行5列 变 5行3列，和reshape(5,3)的区别，reshape只是改变形状，并没有行列互换
# array([[9, 4, 0],
       [3, 9, 7],
       [3, 2, 9],
       [7, 8, 6],
       [2, 2, 3]])
        
np.transpose(a,(1,0)) # 行0，列1。默认情况下是(0,1)----->调整(1,0)
# array([[9, 4, 0],
       [3, 9, 7],
       [3, 2, 9],
       [7, 8, 6],
       [2, 2, 3]])

b = np.random.randint(0,10,size=(1,2,3)) # 三维数组，1行 2行 3列，维度对应0，1，2
b
# array([[[1, 8, 6],
        [4, 2, 1]]])

np.transpose(b,(2,1,0)) # 转置后，3行 2行 1列
# array([[[1],
        [4]],

       [[8],
        [2]],

       [[6],
        [1]]])
```

## 数组堆叠合并

```python
# shape一致才能合并
nd1 = np.random.randint(0,10,size=(3,5))
nd2 = np.random.randint(0,10,size=(3,5))
display(nd1,nd2)
# array([[9, 8, 8, 3, 0],
       [2, 6, 8, 1, 5],
       [6, 7, 0, 8, 5]])
# array([[7, 3, 8, 9, 1],
       [3, 9, 7, 7, 9],
       [0, 0, 3, 5, 3]])

np.concatenate([nd1,nd2]) # 默认axis=0,行方向合并，行增加
# array([[9, 8, 8, 3, 0],
       [2, 6, 8, 1, 5],
       [6, 7, 0, 8, 5],
       [7, 3, 8, 9, 1],
       [3, 9, 7, 7, 9],
       [0, 0, 3, 5, 3]])
      
# 修改axis参数调整数据合并方向
np.concatenate([nd1,nd2],axis=1) # 列增加 axis 轴，0 = 行，1 = 列
# array([[9, 8, 8, 3, 0, 7, 3, 8, 9, 1],
       [2, 6, 8, 1, 5, 3, 9, 7, 7, 9],
       [6, 7, 0, 8, 5, 0, 0, 3, 5, 3]])

np.hstack((nd1,nd2)) # 水平堆叠，合并，h表示水平，列增多
# array([[9, 8, 8, 3, 0, 7, 3, 8, 9, 1],
       [2, 6, 8, 1, 5, 3, 9, 7, 7, 9],
       [6, 7, 0, 8, 5, 0, 0, 3, 5, 3]])

np.vstack((nd1,nd2)) # 垂直堆叠，合并，v表示垂直，行增多
# array([[9, 8, 8, 3, 0],
       [2, 6, 8, 1, 5],
       [6, 7, 0, 8, 5],
       [7, 3, 8, 9, 1],
       [3, 9, 7, 7, 9],
       [0, 0, 3, 5, 3]])
      
a = np.random.randint(0,10,size=(3,5))
b = np.random.randint(0,10,size=(3,6))
display(a,b)
array([[8, 1, 9, 6, 1],
       [4, 4, 1, 1, 7],
       [2, 0, 2, 5, 9]])
array([[6, 3, 1, 1, 0, 8],
       [8, 0, 8, 7, 4, 2],
       [3, 2, 3, 9, 8, 9]])

np.concatenate([a,b]) # 行合并时，列shape不一致，报错
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-181-bdfed7e4c0c5> in <module>
----> 1 np.concatenate([a,b])...

np.concatenate([a,b],axis=1) # 列合并时，行shape一致，可以合并
array([[8, 1, 9, 6, 1, 6, 3, 1, 1, 0, 8],
       [4, 4, 1, 1, 7, 8, 0, 8, 7, 4, 2],
       [2, 0, 2, 5, 9, 3, 2, 3, 9, 8, 9]])
```

## 数组拆分

```python
a = np.random.randint(0,100,size=(6,4))
a
# array([[ 1, 85, 26, 20],
       [57, 82, 21, 29],
       [61, 29, 67, 67],
       [89, 76, 60, 82],
       [78, 41, 70, 65],
       [76, 69,  2, 88]])
      
# indices_or_sections为数值时，表示平均分成几份，不能平均分就会报错
# axis默认=0，默认行切割，这里原数组15行，平均分3份，可以均分。如果改成平均分2份就会报错。
np.split(a,indices_or_sections=3) 
[array([[ 1, 85, 26, 20],
        [57, 82, 21, 29]]),
 array([[61, 29, 67, 67],
        [89, 76, 60, 82]]),
 array([[78, 41, 70, 65],
        [76, 69,  2, 88]])]

# axis=1 按列分割，原数组10列，平均分2份，可以均分。
np.split(a,indices_or_sections=2,axis=1)
[array([[ 1, 85],
        [57, 82],
        [61, 29],
        [89, 76],
        [78, 41],
        [76, 69]]),
 array([[26, 20],
        [21, 29],
        [67, 67],
        [60, 82],
        [70, 65],
        [ 2, 88]])]

# indices_or_sections为列表时，根据索引切片分割
np.split(a,indices_or_sections=[1,3,5]) # 0，1-2，3-4，5-最后 左闭右开
[array([[ 1, 85, 26, 20]]),
 array([[57, 82, 21, 29],
        [61, 29, 67, 67]]),
 array([[89, 76, 60, 82],
        [78, 41, 70, 65]]),
 array([[76, 69,  2, 88]])]

np.hsplit(a,indices_or_sections=2) # h水平，去切水平的东西，切的方向是垂直的，所以按列切
[array([[ 1, 85],
        [57, 82],
        [61, 29],
        [89, 76],
        [78, 41],
        [76, 69]]),
 array([[26, 20],
        [21, 29],
        [67, 67],
        [60, 82],
        [70, 65],
        [ 2, 88]])]

np.vsplit(a,indices_or_sections=3) # v垂直，切垂直的东西，切的方向是水平的，所以按行切
[array([[ 1, 85, 26, 20],
        [57, 82, 21, 29]]),
 array([[61, 29, 67, 67],
        [89, 76, 60, 82]]),
 array([[78, 41, 70, 65],
        [76, 69,  2, 88]])]
```

# Numpy广播机制

广播需要数组形状对齐

```python
np.array([0,1,2,3]*3)
# array([0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3])

arr1 = np.array([0,1,2,3]*3).reshape(4,3)
arr1
#array([[0, 1, 2],
       [3, 0, 1],
       [2, 3, 0],
       [1, 2, 3]])
      
arr1 = np.array([0,1,2,3]*3)
display(arr1)
#array([0, 1, 2, 3, 0, 1, 2, 3, 0, 1, 2, 3])
arr1.sort() # 排序，从小到大
display(arr1)
#array([0, 0, 0, 1, 1, 1, 2, 2, 2, 3, 3, 3])
arr1 = arr1.reshape(4,3)
display(arr1)
#array([[0, 0, 0],
       [1, 1, 1],
       [2, 2, 2],
       [3, 3, 3]])
      
arr2 = np.array([1,2,3])
display(arr1,arr2) # 形状不对应，依然可以进行运算：NumPy底层，为我们进行了广播
# array([[0, 0, 0],
       [1, 1, 1],
       [2, 2, 2],
       [3, 3, 3]])
# array([1, 2, 3])

# 行不够，广播行
arr1 + arr2 # arr2 和 arr1中每一行，进行相加：广播机制
# array([[1, 2, 3],
       [2, 3, 4],
       [3, 4, 5],
       [4, 5, 6]])

arr3 = np.array([[1],[2],[3],[4]])
display(arr1,arr3)
# array([[0, 0, 0],
       [1, 1, 1],
       [2, 2, 2],
       [3, 3, 3]])
# array([[1],
       [2],
       [3],
       [4]])
      
# 列不够，广播列
arr1 + arr3 # arr3 和 arr1中每一列，进行相加：广播
# array([[1, 1, 1],
       [3, 3, 3],
       [5, 5, 5],
       [7, 7, 7]])

# 三维数组广播
a = np.array([0,1,2,3,4,5,6,7]*3).reshape(3,4,2)
a
# array([[[0, 1],
        [2, 3],
        [4, 5],
        [6, 7]],

       [[0, 1],
        [2, 3],
        [4, 5],
        [6, 7]],

       [[0, 1],
        [2, 3],
        [4, 5],
        [6, 7]]])

b = np.array([0,1,2,3,4,5,6,7]).reshape(4,2)
b
# array([[0, 1],
       [2, 3],
       [4, 5],
       [6, 7]])
      
display(a.shape,b.shape)
# (3, 4, 2)
# (4, 2)

a + b # b形状（4，2），b广播了3份
# array([[[ 0,  2],
        [ 4,  6],
        [ 8, 10],
        [12, 14]],

       [[ 0,  2],
        [ 4,  6],
        [ 8, 10],
        [12, 14]],

       [[ 0,  2],
        [ 4,  6],
        [ 8, 10],
        [12, 14]]])
```

# Numpy函数

## 元素级数字函数

```python
# abs、sqrt、square、exp、log、sin、cos、tan，maxinmum、minimum、
# all、any、inner、clip、 round、trace、ceil、floor

a = np.array([-1,-3,-5,1,5,8,9])
a
# array([-1, -3, -5,  1,  5,  8,  9])
np.abs(a) # 求绝对值
# array([1, 3, 5, 1, 5, 8, 9])

b = np.array([1,4,9,16,25,36])
np.sqrt(b) # 求算数平方根
# array([1., 2., 3., 4., 5., 6.])

np.square(b) # 求平方
# array([   1,   16,   81,  256,  625, 1296])

np.exp(2) # 求自然底数e的多少次幂
# 7.38905609893065

np.log(10) # 求自然底数e的对数
# 2.302585092994046

np.sin(np.pi/2) # 求正弦
# 1.0

np.cos(np.pi/2) # 求余弦
# 6.123233995736766e-17

np.tan(np.pi/2) # 求正切
# 1.633123935319537e+16

c = np.random.randint(-10,10,size = 7)
display(a,c)
display(np.maximum(a,c)) # 从a和c中选最大的值
display(np.minimum(a,c)) # 从a和c中选最小的值
# array([-1, -3, -5,  1,  5,  8,  9])
# array([-6,  8, -6, -8,  6,  7, -6])
# array([-1,  8, -5,  1,  6,  8,  9])
# array([-6, -3, -6, -8,  5,  7, -6])

nd1 = np.array([1,3,0]) # 0对应False，非0对应True
nd2 = np.array([-1,-3,4,8])
display(nd1.any()) # 只要有一个True，返回True
display(nd1.all()) # 全部为True，才返回True
display(nd2.all())
# True
# False
# True

a = np.array([1,2,3,4,5])
b = np.array([1,2,3,4,6])
np.inner(a,b) # 返回两个数组的内积，对应位置相乘，加和 1*1+2*2+3*3+4*4+5*6 = 60
# 60

nd1 = np.random.randint(0,100,size=30)
nd1
# array([95, 18, 45, 77, 93, 34,  6, 28, 60,  8, 54, 39, 44, 85, 91, 19, 65,
       76, 49, 61, 74, 90, 22, 99, 73, 70, 34, 87, 96,  2])
np.clip(nd1,10,80) # 数据裁剪，将小于10的变成10，大于80的变成80
# array([80, 18, 45, 77, 80, 34, 10, 28, 60, 10, 54, 39, 44, 80, 80, 19, 65,
       76, 49, 61, 74, 80, 22, 80, 73, 70, 34, 80, 80, 10])

nd = np.random.randn(10)
nd
# array([ 1.82434494,  0.18005771, -1.276181  ,  1.79530858,  0.69927459,
        0.98542502,  0.42622162,  0.75912761, -0.5531669 , -1.8883649 ])
np.round(nd,2) # 保留两位小数，四舍五入
# array([ 1.82,  0.18, -1.28,  1.8 ,  0.7 ,  0.99,  0.43,  0.76, -0.55,
       -1.89])

np.ceil([1.0,1.1,1.4,1.5,1.9]) # 天花板函数，向上取整
# array([1., 2., 2., 2., 2.])
np.floor([1.9,1.5,1.4,1.1,1.0]) # 地板函数，向下取整
# array([1., 1., 1., 1., 1.])

nd = np.random.randint(0,10,size=(3,3))
nd
# array([[9, 8, 7],
       [9, 4, 1],
       [9, 5, 5]])
np.trace(nd) # 计算对角线上的和,9+4+5
# 18

np.unique(nd) # 去重
```

## where函数

```python
nd1 = np.array([1,3,5,7,9])
nd2 = np.array([2,4,6,8,10])
cond = np.array([True,False,False,True,True])

np.where(cond,nd1,nd2) # 条件为True，返回nd1中的值，False返回nd2中的值
# array([1, 4, 6, 7, 9])

a = np.random.randint(0,100,size=10)
display(a)
np.where(a > 50,a,-100) # 数据>50，返回数据，否则返回-100
# array([33, 37, 80,  4, 46,  7, 37, 58, 77, 87])
# array([-100, -100,   80, -100, -100, -100, -100,   58,   77,   87])
```

## 排序

```python
a = np.random.randint(0,100,size=10)
a
# array([56,  8, 47, 17, 73, 97, 68, 59, 45, 53])

display(np.sort(a)) # 返回从小打到大排序后的数组，不改变原数组
display(a) # 原数组没变
# array([ 8, 17, 45, 47, 53, 56, 59, 68, 73, 97])
# array([56,  8, 47, 17, 73, 97, 68, 59, 45, 53])

a.sort() # 调用数组对象a的sort方法排序，改变原数组，原地排序
a
# array([ 8, 17, 45, 47, 53, 56, 59, 68, 73, 97])

a = np.random.randint(0,100,size=10)
display(a)
display(a.argsort()) # 返回排序的索引
index = a.argsort()
display(a[index]) # 用排序的索引取出数据，实现排序
display(a) # 不改变原数组
# array([47, 21, 19, 28, 90, 62, 72, 97, 43, 50])
# array([2, 1, 3, 8, 0, 9, 5, 6, 4, 7])
# array([19, 21, 28, 43, 47, 50, 62, 72, 90, 97])
# array([47, 21, 19, 28, 90, 62, 72, 97, 43, 50])

# 从大到小排序，利用切片步长为负倒取的特性实现::-1
display(a)
a.sort()
display(a)
display(a[::-1])
# array([47, 21, 19, 28, 90, 62, 72, 97, 43, 50])
# array([19, 21, 28, 43, 47, 50, 62, 72, 90, 97])
# array([97, 90, 72, 62, 50, 47, 43, 28, 21, 19])
```

## 集合操作

```python
a = np.random.randint(0,30,size=15)
b = np.random.randint(0,30,size=15)
display(a,b)
# array([14,  6, 20,  0, 15, 13, 13, 10, 17,  1, 17, 17,  6, 23, 13])
# array([14, 12, 21, 13,  9, 18,  7,  6, 14, 20,  9, 17, 25, 24, 21])

np.intersect1d(a,b) # 交集
# array([ 6, 13, 14, 17, 20])

np.union1d(a,b) # 并集
# array([ 0,  1,  6,  7,  9, 10, 12, 13, 14, 15, 17, 18, 20, 21, 23, 24, 25])

np.setdiff1d(a,b) # 差集 a有 b没有
# array([ 0,  1, 10, 15, 23])
np.setdiff1d(b,a) # 差级 b有 a没有
# array([ 7,  9, 12, 18, 21, 24, 25])
```

## 数学和统计函数

```python
# min、max、mean、median、sum、std、var、
# cumsum、cumprod、argmin、argmax、 argwhere、cov、corrcoef

a = np.random.randint(0,100,size=(3,5))
a
# array([[87, 14, 35, 24, 31],
       [42, 62, 83, 88, 64],
       [61, 23,  5, 31, 87]])

a.min() # 全部最小
# 5

a.max() # 全部最大
# 88

a.max(axis=0) # 0维方向最大
# array([87, 62, 83, 88, 87])

a.min(axis=1) # 1维方向最小
# array([14, 42,  5])

a.mean() # 全部平均值
# 49.13333333333333

np.median(a) # 全部中位数
# 42.0

a.sum() # 全部求和
# 737

a.std() # 标准差
# 27.63781628292816

a.var() # 方差，数据内部波动情况
# 763.848888888889

a.cumsum() # 累加和
# array([ 87, 101, 136, 160, 191, 233, 295, 378, 466, 530, 591, 614, 619,
       650, 737])

b = np.array([1,2,3,4,5,6])
b.cumprod() # 累乘和
# array([  1,   2,   6,  24, 120, 720])

a.argmin() # 最小值索引
# 12

a.argmax() # 最大值索引
# 8

np.argwhere(a>50) # 返回满足条件的数据索引，表示第几行第几列
# array([[0, 0],
       [1, 1],
       [1, 2],
       [1, 3],
       [1, 4],
       [2, 0],
       [2, 4]])
for row,col in np.argwhere(a>50):
    print(a[row,col])
87
62
83
88
64
61
87

# cov 协方差，两个属性之间进行计算。与方差概念类似（方差是数据内部或属性内部进行计算）
# 例如：一个男生受女生欢迎程度，和这名男生的猥琐程度，是否成正比，是什么关系，这就是两个属性间的计算，就是协方差
np.cov(a)
# array([[ 807.7, -356.7,  334.4],
       [-356.7,  338.2, -333.9],
       [ 334.4, -333.9, 1058.8]])

np.corrcoef(a) # 相关性系数，范围：1（正相关） ～ -1（负相关），0表示没有关系
# array([[ 1.        , -0.6824821 ,  0.36160486],
       [-0.6824821 ,  1.        , -0.55798516],
       [ 0.36160486, -0.55798516,  1.        ]])
```

# Numpy线性代数

## 矩阵相乘

```python
a = np.random.randint(0,10,size=(3,3))
b = np.random.randint(0,10,size=(3,4))
display(a,b)
# array([[8, 5, 2],
       [1, 5, 4],
       [5, 5, 3]])
# array([[0, 6, 7, 7],
       [8, 0, 2, 9],
       [3, 9, 9, 1]])
    
a.dot(b) # 矩阵乘法，行列相乘，矩阵a的行长度，和矩阵b的列长度，必须相等
# array([[ 46,  66,  84, 103],
       [ 52,  42,  53,  56],
       [ 49,  57,  72,  83]])
    
np.dot(a,b) # 模块提供的方法
# array([[ 46,  66,  84, 103],
       [ 52,  42,  53,  56],
       [ 49,  57,  72,  83]])

a @ b # 也表示矩阵运算
# array([[ 46,  66,  84, 103],
       [ 52,  42,  53,  56],
       [ 49,  57,  72,  83]])
```

# axis的理解

————————————————
版权声明：本文为CSDN博主「Idear_to_dream」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/dream_to_dream/article/details/105121844https://blog.csdn.net/dream_to_dream/article/details/105121844?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)

#### 1.Axis就是数组层级，亦可以理解为数字的维度，设axis=i，则Numpy沿着第i个下标变化的方向进行操作

#### Axis就是数组层级

要想理解axis，首先我们先要弄清楚“Numpy中数组的维数”和"线性代数中矩阵的维数"这两个概念以及它们之间的关系。在数学或者物理的概念中，dimensions被认为是在空间中表示一个点所需要的最少坐标个数，但是在Numpy中，dimensions指代的是数组的维数。比如下面这个例子：

```python
import numpy as np
a = np.array([[1,2,3],[2,3,4],[3,4,9]])
a
array([[1, 2, 3],
       [2, 3, 4],
       [3, 4, 9]])
```

这个array的维数只有2，即axis轴有两个，分别是axis=0和axis=1。如下图所示，该二维数组的第0维(axis=0)有三个元素(左图)，即axis=0轴的长度length为3；第1维(axis=1)也有三个元素(右图)，即axis=1轴的长度length为3。正是因为axis=0、axis=1的长度都为3，矩阵横着竖着都有3个数，所以该矩阵在线性代数是3维的(rank秩为3)。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi03NmI3MjdhZWRmNzU2ZDAzYzg1NGYzYjA0NTM0NWMyM183MjB3LmpwZw?x-oss-process=image/format,png)

因此，axis就是数组层级。

当axis=0，该轴上的元素有3个(数组的size为3)

a[0]、a[1]、a[2]

当axis=1，该轴上的元素有3个(数组的size为3)

a\[0][0]、a\[0][1]、a\[0][2]

（或者a\[1][0]、a\[1][1]、a\[1][2]）

（或者a\[2][0]、a\[2][1]、a\[2][2]）

 

再比如下面shape为(3,2,4)的array：

```python
b = np.array([[[1,2,3,4],[1,3,4,5]],[[2,4,7,5],[8,4,3,5]],[[2,5,7,3],[1,5,3,7]]])
b
array([[[1, 2, 3, 4],
				[1, 3, 4, 5]],
       
        [[2, 4, 7, 5],
         [8, 4, 3, 5]],
 
        [[2, 5, 7, 3],
         [1, 5, 3, 7]]])

b.shape
(3, 2, 4)
```
这个shape（用tuple表示）可以理解为在每个轴（axis）上的size，也即占有的长度（length)。为了更进一步理解，我们可以暂时把多个axes想象成多层layers。axis=0表示第一层(下图黑色框框)，该层数组的size为3，对应轴上的元素length = 3；axis=1表示第二层(下图红色框框)，该层数组的size为2，对应轴上的元素length = 2；axis=2表示第三层(下图蓝色框框)，对应轴上的元素length = 4。

 ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi01YzhhYWQ0MGY3NGEzYWRlZTcyZjhmN2ZiNWJlODlhNl83MjB3LmpwZw?x-oss-process=image/format,png)

#### 设axis=i，则Numpy沿着第i个下标变化的方向进行操作

1.二维数组示例：

比如np.sum(a, axis=1)，结合下面的数组， a\[0][0]=1、a\[0][1]=2、a\[0][2]=3 ，下标会发生变化的方向是数组的第一维。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi03YTA3MTYyMzBhNmYzZDQ4NDBhNjA5ODAwMWIxZDJhMl83MjB3LmpwZw?x-oss-process=image/format,png)

我们往下标会变化的方向，把元素相加后即可得到最终结果：

```python
[
  [6],
  [9],
  [16]
]
```

2.三维数组示例：

再举个例子，比如下边这个np.shape(a)=(3,2,4)的3维数组，该数组第0维的长度为3(黑色框框)，再深入一层，第1维的长度为2(红色框框)，再深入一层，第2维的长度为4(蓝色框框)。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi0yMDM2YmVhYmMxYzdlN2Q1ZGM4NDM3YTQ3NTc2NjA1Ml83MjB3LmpwZw?x-oss-process=image/format,png)

如果我们要计算np.sum(a, axis=1)，在第一个黑色框框中，

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi1mNzdhNDMxN2UxZmU2ZjEzOTcxOGFjYjdjODBhM2U4Nl83MjB3LmpwZw?x-oss-process=image/format,png)

下标的变化方向如下所示：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMyLnpoaW1nLmNvbS84MC92Mi00YjMwM2Q0YzM3NWVkMzVmMmUzYTMzNjI4N2IwOTgwZF83MjB3LmpwZw?x-oss-process=image/format,png)

所以，我们要把上下两个红色框框相加起来

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMxLnpoaW1nLmNvbS84MC92Mi02YWIyZTAzNTFhMmE4NDIwNGJlNzlhY2ZlN2MzOTlkOF83MjB3LmpwZw?x-oss-process=image/format,png)

 

按照同样的逻辑处理第二个和第三个黑色的框框，可以得出最终结果：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWM0LnpoaW1nLmNvbS84MC92Mi1iZTA5NDQyYjZhZDg3Zjk2OTMyNDEyYzViOGFiNmZlM183MjB3LmpwZw?x-oss-process=image/format,png)

所以，依然是我们前边总结的那一句话，设axis=i，则Numpy沿着第i个下标变化的方向进行操作。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMyLnpoaW1nLmNvbS84MC92Mi00YjMwM2Q0YzM3NWVkMzVmMmUzYTMzNjI4N2IwOTgwZF83MjB3LmpwZw?x-oss-process=image/format,png)

————————————————
版权声明：本文为CSDN博主「Idear_to_dream」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/dream_to_dream/article/details/105121844