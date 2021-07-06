---
title: 【数据分析】第五阶段：python（四）Pandas数据分析库 上
date: 2021-06-28 11:42:17
tags:
- 数据分析
- python
- Pandas
categories:
- 学习笔记
description: andas科学计算库实现数据预处理和数据分析，创建Series和DataFrame用于多种格式的读写以及数据转换。
---

# pandas介绍和安装

- Python在数据处理和准备方面一直做得很好，但在数据分析和建模方面就差一些。pandas帮助填 补了这一空白，使您能够在Python中执行整个数据分析工作流程，而不必切换到更特定于领域的语 言，如R。
- 与出色的 jupyter工具包和其他库相结合，Python中用于进行数据分析的环境在性能、生产率和协 作能力方面都是卓越的。
- pandas是 Python 的核心数据分析支持库，提供了快速、灵活、明确的数据结构，旨在简单、直观 地处理关系型、标记型数据。pandas是Python进行数据分析的必备高级工具。
-  pandas的主要数据结构是 **Series(**一维数据)与 **DataFrame** (二维数据)，这两种数据结构足以处理 金融、统计、社会科学、工程等领域里的大多数案例。
- 处理数据一般分为几个阶段:数据整理与清洗、数据分析与建模、数据可视化与制表，Pandas 是 处理数据的理想工具。

```bash
pip3 install pandas
```

# pandas数据结构

## Series

In [1]:

```
import numpy as np
import pandas as pd # pandas基于numpy，使用pandas必须先引入numpy
```

In [2]:

```python
# Series
l = np.array([1,2,3,6,9]) # Numpy数组

s1 = pd.Series(data = l)

display(l,s1)  # Series是一维的数组，和NumPy数组不一样：Series多了索引，创建的时候没有指明索引，默认从0开始
array([1, 2, 3, 6, 9])
0    1
1    2
2    3
3    6
4    9
dtype: int64
```

In [3]:

```python
s2 = pd.Series(data = l, index = list('ABCDE')) # 创建的时候指明索引
s2
```

Out[3]:

```
A    1
B    2
C    3
D    6
E    9
dtype: int64
```

In [4]:

```python
# Series也可以以键值对的形式构建，键作为Series的索引，值作为Series的值
s3 = pd.Series(data = {'A':149, 'B':130, 'C':118, 'D':99, 'E':66})
s3
```

Out[4]:

```
A    149
B    130
C    118
D     99
E     66
dtype: int64
```

## DataFrame

In [6]:

```python
# Series是一维的，功能比较少
# DataFrame是二维的，多个Series公用索引，组成了DataFrame 
df1 = pd.DataFrame(
    data = np.random.randint(0,151,size=(10,3)),
    index = list('ABCDEFGHIJ'), # 行索引
    columns = ['Python','Math','En'] # 列索引
)
df1
```

Out[6]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |    133 |   30 |  132 |
|    B |      0 |   53 |   58 |
|    C |     99 |  125 |  103 |
|    D |     30 |   28 |   11 |
|    E |     77 |   64 |  114 |
|    F |     50 |   11 |   94 |
|    G |     91 |   15 |   16 |
|    H |    115 |  123 |   89 |
|    I |     16 |   65 |   46 |
|    J |     64 |   40 |   18 |

In [7]:

```python
df2 = pd.DataFrame(data = {'Python':[66,99,128], 'Math':[88,65,137], 'En':[100,121,45]})
df2 # 字典，key作为列索引，index不指定默认从0开始索引
```

Out[7]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     66 |   88 |  100 |
|    1 |     99 |   65 |  121 |
|    2 |    128 |  137 |   45 |

# 常用数据查看属性

In [8]:

```python
df = pd.DataFrame(
    data = np.random.randint(0,151,size=(100,3)),
    columns=['Python','Math','En']
)
df
```

Out[8]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |    105 |    7 |   85 |
|    1 |    103 |  125 |   89 |
|    2 |     59 |   94 |  112 |
|    3 |     62 |  148 |   11 |
|    4 |     39 |   89 |   30 |
|  ... |    ... |  ... |  ... |
|   95 |     77 |   95 |   95 |
|   96 |    136 |   77 |   97 |
|   97 |     56 |   10 |   20 |
|   98 |    114 |   11 |   84 |
|   99 |    141 |   42 |   39 |

100 rows × 3 columns

In [10]:

```python
df.shape # 查看DataFrame的形状
```

Out[10]:

```
(100, 3)
```

In [13]:

```python
df.head(n = 3) # 显示前n个，默认n=5省略
```

Out[13]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |    105 |    7 |   85 |
|    1 |    103 |  125 |   89 |
|    2 |     59 |   94 |  112 |

In [15]:

```python
df.tail() # 显示后n个，默认n=5
```

Out[15]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|   95 |     77 |   95 |   95 |
|   96 |    136 |   77 |   97 |
|   97 |     56 |   10 |   20 |
|   98 |    114 |   11 |   84 |
|   99 |    141 |   42 |   39 |

In [16]:

```python
df.dtypes # 数据类型
```

Out[16]:

```
Python    int64
Math      int64
En        int64
dtype: object
```

In [17]:

```python
df.info() # 详细信息
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 100 entries, 0 to 99
Data columns (total 3 columns):
 #   Column  Non-Null Count  Dtype
---  ------  --------------  -----
 0   Python  100 non-null    int64
 1   Math    100 non-null    int64
 2   En      100 non-null    int64
dtypes: int64(3)
memory usage: 2.5 KB
```

In [18]:

```python
df.describe() # 描述数据的统计信息：平均值，标准差，中位数，四等分，最大值，最小值
```

Out[18]:

|       |     Python |       Math |         En |
| ----: | ---------: | ---------: | ---------: |
| count | 100.000000 | 100.000000 | 100.000000 |
|  mean |  74.430000 |  78.520000 |  71.810000 |
|   std |  41.493642 |  44.733437 |  42.361288 |
|   min |   0.000000 |   0.000000 |   1.000000 |
|   25% |  38.750000 |  41.250000 |  33.250000 |
|   50% |  72.500000 |  80.000000 |  78.500000 |
|   75% | 108.250000 | 118.750000 | 106.250000 |
|   max | 147.000000 | 150.000000 | 146.000000 |

In [20]:

```python
df.values # 值，返回NumPy数组
```

. . .

In [21]:

```python
df.columns # 列索引
```

Out[21]:

```
Index(['Python', 'Math', 'En'], dtype='object')
```

In [22]:

```python
df.index # 行索引
```

Out[22]:

```
RangeIndex(start=0, stop=100, step=1)
```

# 数据的输入与输出

## csv

```python
df = pd.DataFrame(
    data = np.random.randint(0,151,size=(5,3)),
    columns=['Python','Math','En']
)
df
```

Out[31]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     44 |   30 |  120 |
|    1 |      6 |  102 |  108 |
|    2 |    142 |   19 |   71 |
|    3 |     80 |  146 |   74 |
|    4 |     63 |  123 |   57 |

In [32]:

```python
# 保存数据为csv文件
df.to_csv('./data.csv',
          sep=',', # 指定分隔符
          index=True, # 是否保存行索引，默认True
          header=True # 是否保存列索引，默认True
         )

,Python,Math,En
0,44,30,120
1,6,102,108
2,142,19,71
3,80,146,74
4,63,123,57
```

In [33]:

```python
# 读取csv文件数据
pd.read_csv('./data.csv',
            index_col=0 # 第一列作为行索引，如果不指定默认会添加行索引，原来的行索引会变成数据列
           )
```

Out[33]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     44 |   30 |  120 |
|    1 |      6 |  102 |  108 |
|    2 |    142 |   19 |   71 |
|    3 |     80 |  146 |   74 |
|    4 |     63 |  123 |   57 |

In [39]:

```
pd.read_csv('./data.csv')
```

Out[39]:

|      | Unnamed: 0 | Python | Math |   En |
| ---: | ---------: | -----: | ---: | ---: |
|    0 |          0 |     44 |   30 |  120 |
|    1 |          1 |      6 |  102 |  108 |
|    2 |          2 |    142 |   19 |   71 |
|    3 |          3 |     80 |  146 |   74 |
|    4 |          4 |     63 |  123 |   57 |

In [34]:

```python
pd.read_csv('./data.csv',
            index_col=0,
            header=None # 默认第一行作为列索引，指明header=None，原来的列索引变成数据第一行，生成新的列索引
           )
```

Out[34]:

|      |      1 |    2 |    3 |
| ---: | -----: | ---: | ---: |
|    0 |        |      |      |
|  NaN | Python | Math |   En |
|  0.0 |     44 |   30 |  120 |
|  1.0 |      6 |  102 |  108 |
|  2.0 |    142 |   19 |   71 |
|  3.0 |     80 |  146 |   74 |
|  4.0 |     63 |  123 |   57 |

## Excel

In [35]:

```
df
```

Out[35]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     44 |   30 |  120 |
|    1 |      6 |  102 |  108 |
|    2 |    142 |   19 |   71 |
|    3 |     80 |  146 |   74 |
|    4 |     63 |  123 |   57 |

In [36]:

```
# 存储数据到excel
df.to_excel('./data.xls')
```

In [38]:

```
# 读取excel数据
pd.read_excel('./data.xls',index_col=0)
```

Out[38]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     44 |   30 |  120 |
|    1 |      6 |  102 |  108 |
|    2 |    142 |   19 |   71 |
|    3 |     80 |  146 |   74 |
|    4 |     63 |  123 |   57 |

## HDF5

HDF5是一个独特的技术套件，可以管理非常大和复杂的数据收集。

 HDF5，可以存储不同类型数据的文件格式，后缀通常是.h5，它的结构是层次性的。 

一个HDF5文件可以被看作是一个组包含了各类不同的数据集。

对于HDF5文件中的数据存储，有两个核心概念:group 和 dataset

dataset 代表数据集，一个文件当中可以存放不同种类的数据集，这些数据集如何管理，就用到了group

最直观的理解，可以参考我们的文件管理系统，不同的文件位于不同的目录下。

目录就是HDF5中的group, 描述了数据集dataset的分类信息，通过group 有效的将多种dataset 进行管 理和区分;文件就是HDF5中的dataset, 表示的是具体的数据。

需要安装tables库

```python
# 存储数据为hdf5
df.to_hdf('./data.h5', key='score') # key相当于文件夹名
```

In [27]:

```python
df2 = pd.DataFrame(data = np.random.randint(0,100,size=(10,3)),
                   columns=['身高','体重','视力']
                  )
df2.to_hdf('./data.h5', key='info')
```

In [28]:

```python
# 读取hdf5数据
pd.read_hdf('./data.h5',key='score') # 相当于读取对应文件夹下的数据
```

Out[28]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    0 |     61 |   53 |  144 |
|    1 |     40 |  119 |   55 |
|    2 |     25 |   12 |   60 |
|    3 |     57 |   70 |    0 |
|    4 |     56 |   68 |  122 |

In [29]:

```python
pd.read_hdf('./data.h5',key='info')
```

Out[29]:

|      | 身高 | 体重 | 视力 |
| ---: | ---: | ---: | ---: |
|    0 |   74 |   99 |   76 |
|    1 |   34 |   23 |   32 |
|    2 |   91 |   35 |   92 |
|    3 |   79 |   14 |   32 |
|    4 |   71 |   12 |   96 |
|    5 |   91 |   78 |   49 |
|    6 |   23 |   49 |   28 |
|    7 |   40 |    8 |   23 |
|    8 |   71 |   96 |   16 |
|    9 |   59 |   62 |   96 |

### 坑

pip3 install tables报错，could not find a local hdf5之类的，原因是没有安装hdf5，用brew install hdf5即可。

完成hdf5的安装，再次执行pip3 install tables，依然报错，报错信息error: command '/usr/bin/clang' failed with exit status 1，原因python版本和tables版本不匹配，python版本3.9过高。解决办法：brew重新安装低版本python3.7，使用低版本python3.7作为默认解释器，再重新安装即可成功。

Mac系统自带python路径为/System/Library/Frameworks/Python.framework/Version 这里可能会有多个python版本，里面Current存放系统当前python版本。

HomeBrew安装python路径为/usr/local/Cellar/python 里面存放HomeBrew所安装版本。

系统命令默认路径在/usr/bin，用户命令默认路径在/usr/local/bin（brew安装的命令默认在这个路径下） 。

[Mac使用HomeBrew安装Python3.*并设为默认版本](https://www.jianshu.com/p/44de7261d5b0)

## SQL

需要安装三方库：

pip install sqlalchemy -i https://pypi.tuna.tsinghua.edu.cn/simple

pip install pymysql -i https://pypi.tuna.tsinghua.edu.cn/simple

```python
from sqlalchemy import create_engine # 数据库引擎，构建和数据库的连接
```

In [38]:

```python
# 这就是一个数据库连接
# mysql+pymysql://数据库用户名:密码@数据库地址/数据库名?参数设置
engine = create_engine('mysql+pymysql://root:6203021412@localhost/pandas?charset=utf8')
```

In [39]:

```python
# 存储数据到mysql
# name表名，con数据库连接，index是否存储行索引，默认True
df2.to_sql(name='info', con=engine, index=False)
```

In [40]:

```python
# 读取mysql数据
pd.read_sql('select * from info', con=engine)
```

Out[40]:

|      | 身高 | 体重 | 视力 |
| ---: | ---: | ---: | ---: |
|    0 |   74 |   99 |   76 |
|    1 |   34 |   23 |   32 |
|    2 |   91 |   35 |   92 |
|    3 |   79 |   14 |   32 |
|    4 |   71 |   12 |   96 |
|    5 |   91 |   78 |   49 |
|    6 |   23 |   49 |   28 |
|    7 |   40 |    8 |   23 |
|    8 |   71 |   96 |   16 |
|    9 |   59 |   62 |   96 |

# 数据选取

## 获取数据

In [33]:

```python
df = pd.DataFrame(
    data=np.random.randint(0,151,size=(5,3)),
    index=list('ABCDE'),
    columns=['Python','Math','En']
)
df
```

Out[33]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |      4 |   47 |  104 |
|    B |     72 |  108 |  124 |
|    C |    102 |   50 |   20 |
|    D |    143 |   46 |  123 |
|    E |     11 |   18 |   94 |

In [34]:

```python
df['Python'] # 获取Python列数据
```

Out[34]:

```
A      4
B     72
C    102
D    143
E     11
Name: Python, dtype: int64
```

In [35]:

```python
df.Python # 同df['Python']，DataFrame中，列索引是属性，dir(df)
```

Out[35]:

```
A      4
B     72
C    102
D    143
E     11
Name: Python, dtype: int64
```

In [36]:

```python
df[['Python','Math']] # 获取多列数据
```

Out[36]:

|      | Python | Math |
| ---: | -----: | ---: |
|    A |      4 |   47 |
|    B |     72 |  108 |
|    C |    102 |   50 |
|    D |    143 |   46 |
|    E |     11 |   18 |

## 标签选择

In [37]:

```python
# 标签，就是行索引 location = loc位置，loc[]里只能写行索引值，写列索引值会报错
df.loc['A']
```

Out[37]:

```
Python      4
Math       47
En        104
Name: A, dtype: int64
```

In [38]:

```python
df.loc[['A','B','C']] # 获取多行数据
```

Out[38]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |      4 |   47 |  104 |
|    B |     72 |  108 |  124 |
|    C |    102 |   50 |   20 |

In [39]:

```python
df.loc['A','Python'] # 获取某行，某列的数据 [[行1,行2...],[列1,列2,...]]
```

Out[39]:

```
4
```

In [40]:

```python
df.loc[['A','B'],['Python','Math']]
```

Out[40]:

|      | Python | Math |
| ---: | -----: | ---: |
|    A |      4 |   47 |
|    B |     72 |  108 |

In [41]:

```python
df.loc['A'::2,'Python']
```

Out[41]:

```
A      4
C    102
E     11
Name: Python, dtype: int64
```

In [42]:

```python
df.loc['A':'D',:]
```

Out[42]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |      4 |   47 |  104 |
|    B |     72 |  108 |  124 |
|    C |    102 |   50 |   20 |
|    D |    143 |   46 |  123 |

## 位置选择

In [43]:

```python
# 位置就是数字的索引，从0开始
df.iloc[0]
```

Out[43]:

```
Python      4
Math       47
En        104
Name: A, dtype: int64
```

In [44]:

```python
df.iloc[[0,2,4]]
```

Out[44]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |      4 |   47 |  104 |
|    C |    102 |   50 |   20 |
|    E |     11 |   18 |   94 |

In [46]:

```python
df.iloc[[0,2],[1,2]] # 行列都要用数字
```

Out[46]:

|      | Math |   En |
| ---: | ---: | ---: |
|    A |   47 |  104 |
|    C |   50 |   20 |

In [45]:

```python
df.iloc[:4:2,::2]
```

Out[45]:

|      | Python |   En |
| ---: | -----: | ---: |
|    A |      4 |  104 |
|    C |    102 |   20 |

## boolean索引

In [47]:

```python
df > 75
```

Out[47]:

|      | Python |  Math |    En |
| ---: | -----: | ----: | ----: |
|    A |  False | False |  True |
|    B |  False |  True |  True |
|    C |   True | False | False |
|    D |   True | False |  True |
|    E |  False | False |  True |

In [48]:

```python
df[df>75]
```

Out[48]:

|      | Python |  Math |    En |
| ---: | -----: | ----: | ----: |
|    A |    NaN |   NaN | 104.0 |
|    B |    NaN | 108.0 | 124.0 |
|    C |  102.0 |   NaN |   NaN |
|    D |  143.0 |   NaN | 123.0 |
|    E |    NaN |   NaN |  94.0 |

In [49]:

```python
# 选出python成绩>80分的数据
df['Python'] > 80
```

Out[49]:

```
A    False
B    False
C     True
D     True
E    False
Name: Python, dtype: bool
```

In [50]:

```python
cond = df['Python'] > 80
df[cond]
```

Out[50]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    C |    102 |   50 |   20 |
|    D |    143 |   46 |  123 |

In [51]:

```python
 # 选出平均成绩 > 75的数据
cond = df.mean(axis=1) > 75
df[cond]
```

Out[51]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    B |     72 |  108 |  124 |
|    D |    143 |   46 |  123 |

In [52]:

```python
# 选出python>70 并且 math>70 的数据
cond = (df['Python'] > 70) & (df['Math'] > 70)
df[cond]
```

Out[52]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    B |     72 |  108 |  124 |

In [53]:

```python
# index.isin() 行索引是否在指定的数组中
display(df.index)
display(df.index.isin(['B','D']))
cond = df.index.isin(['B','D'])
df[cond]
Index(['A', 'B', 'C', 'D', 'E'], dtype='object')
array([False,  True, False,  True, False])
```

Out[53]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    B |     72 |  108 |  124 |
|    D |    143 |   46 |  123 |

## 赋值操作

In [54]:

```python
df
```

Out[54]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |      4 |   47 |  104 |
|    B |     72 |  108 |  124 |
|    C |    102 |   50 |   20 |
|    D |    143 |   46 |  123 |
|    E |     11 |   18 |   94 |

In [55]:

```python
df['Python']['A'] = 150 # 修改某个位置的值，列索引是属性可以直接访问,行索引又是列索引属性返回值的属性，可以使用dir查看
```

In [56]:

```python
df
```

Out[56]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |    150 |   47 |  104 |
|    B |     72 |  108 |  124 |
|    C |    102 |   50 |   20 |
|    D |    143 |   46 |  123 |
|    E |     11 |   18 |   94 |

In [57]:

```python
df['Java'] = np.random.randint(0,151,size = 5) # 新增加一列
df
```

Out[57]:

|      | Python | Math |   En | Java |
| ---: | -----: | ---: | ---: | ---: |
|    A |    150 |   47 |  104 |  124 |
|    B |     72 |  108 |  124 |  133 |
|    C |    102 |   50 |   20 |   39 |
|    D |    143 |   46 |  123 |   31 |
|    E |     11 |   18 |   94 |  150 |

In [58]:

```python
df.loc[['B','C','D'],'Math'] = 147 # 修改多个成绩
df
```

Out[58]:

|      | Python | Math |   En | Java |
| ---: | -----: | ---: | ---: | ---: |
|    A |    150 |   47 |  104 |  124 |
|    B |     72 |  147 |  124 |  133 |
|    C |    102 |  147 |   20 |   39 |
|    D |    143 |  147 |  123 |   31 |
|    E |     11 |   18 |   94 |  150 |

In [59]:

```python
cond = df < 60
df[cond] = 60 # 小于60的都改为60，不符合的不改变
df
```

Out[59]:

|      | Python | Math |   En | Java |
| ---: | -----: | ---: | ---: | ---: |
|    A |    150 |   60 |  104 |  124 |
|    B |     72 |  147 |  124 |  133 |
|    C |    102 |  147 |   60 |   60 |
|    D |    143 |  147 |  123 |   60 |
|    E |     60 |   60 |   94 |  150 |

In [60]:

```python
df += 1000 # +=操作
df
```

Out[60]:

|      | Python | Math |   En | Java |
| ---: | -----: | ---: | ---: | ---: |
|    A |   1150 | 1060 | 1104 | 1124 |
|    B |   1072 | 1147 | 1124 | 1133 |
|    C |   1102 | 1147 | 1060 | 1060 |
|    D |   1143 | 1147 | 1123 | 1060 |
|    E |   1060 | 1060 | 1094 | 1150 |

# 数据集成

## concat数据串联

In [71]:

```python
# np.concatenate NumPy数据集成
df1 = pd.DataFrame(
    data=np.random.randint(0,10,size=(3,3)),
    index=list('ABC'),
    columns=['Py','Math','En']
)
df2 = pd.DataFrame(
    data=np.random.randint(0,10,size=(3,3)),
    index=list('XYZ'),
    columns=['Py','Math','En']
)
df3 = pd.DataFrame(
    data=np.random.randint(0,10,size=(3,2)),
    index=list('ABC'),
    columns=['Java','JS']
)
```

In [72]:

```python
pd.concat([df1,df2]) # 默认axis=0，0维方向合并
```

Out[72]:

|      |   Py | Math |   En |
| ---: | ---: | ---: | ---: |
|    A |    1 |    3 |    8 |
|    B |    7 |    0 |    5 |
|    C |    4 |    2 |    8 |
|    X |    0 |    2 |    4 |
|    Y |    0 |    6 |    5 |
|    Z |    8 |    0 |    1 |

In [73]:

```python
pd.concat([df1,df3], axis=1) # axis=1，1维方向合并
```

Out[73]:

|      |   Py | Math |   En | Java |   JS |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    A |    1 |    3 |    8 |    1 |    6 |
|    B |    7 |    0 |    5 |    5 |    6 |
|    C |    4 |    2 |    8 |    3 |    7 |

In [74]:

```python
pd.concat([df1,df3]) # 合并的两个数据，索引不对齐时，出现空数据
```

Out[74]:

|      |   Py | Math |   En | Java |   JS |
| ---: | ---: | ---: | ---: | ---: | ---: |
|    A |  1.0 |  3.0 |  8.0 |  NaN |  NaN |
|    B |  7.0 |  0.0 |  5.0 |  NaN |  NaN |
|    C |  4.0 |  2.0 |  8.0 |  NaN |  NaN |
|    A |  NaN |  NaN |  NaN |  1.0 |  6.0 |
|    B |  NaN |  NaN |  NaN |  5.0 |  6.0 |
|    C |  NaN |  NaN |  NaN |  3.0 |  7.0 |

In [75]:

```python
pd.concat([df1,df2],axis=1)
```

Out[75]:

|      |   Py | Math |   En |   Py | Math |   En |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|    A |  1.0 |  3.0 |  8.0 |  NaN |  NaN |  NaN |
|    B |  7.0 |  0.0 |  5.0 |  NaN |  NaN |  NaN |
|    C |  4.0 |  2.0 |  8.0 |  NaN |  NaN |  NaN |
|    X |  NaN |  NaN |  NaN |  0.0 |  2.0 |  4.0 |
|    Y |  NaN |  NaN |  NaN |  0.0 |  6.0 |  5.0 |
|    Z |  NaN |  NaN |  NaN |  8.0 |  0.0 |  1.0 |

## append追加数据

In [76]:

```python
df1.append(df2)
```

Out[76]:

|      |   Py | Math |   En |
| ---: | ---: | ---: | ---: |
|    A |    1 |    3 |    8 |
|    B |    7 |    0 |    5 |
|    C |    4 |    2 |    8 |
|    X |    0 |    2 |    4 |
|    Y |    0 |    6 |    5 |
|    Z |    8 |    0 |    1 |

## insert数据插入

In [77]:

```python
df1
```

Out[77]:

|      |   Py | Math |   En |
| ---: | ---: | ---: | ---: |
|    A |    1 |    3 |    8 |
|    B |    7 |    0 |    5 |
|    C |    4 |    2 |    8 |

In [78]:

```python
# 插入列
df1.insert(
    loc=1, # 插入的位置
    column='Java', # 插入的列索引值
    value=np.random.randint(0,10,size=3) # 插入列的值
)
df1
```

Out[78]:

|      |   Py | Java | Math |   En |
| ---: | ---: | ---: | ---: | ---: |
|    A |    1 |    7 |    3 |    8 |
|    B |    7 |    4 |    0 |    5 |
|    C |    4 |    5 |    2 |    8 |

## merge方法sql风格join合并

In [79]:

```python
df1 = pd.DataFrame(
    data={
        'name':['Peter','Mike','Ana','John','张三'],
        'height':[189,195,178,177,180]
    }
)
df2 = pd.DataFrame(
    data={
        'name':['Peter','Mike','Ana','John','李四'],
        'weight':[70,63,75,88,67]
    }
)
df3 = pd.DataFrame(
    data={
        '名字':['Peter','Mike','Ana','John','张三'],
        'salary':np.random.randint(20,100,size=5)
    }
)
```

In [80]:

```python
# 根据共同的属性，合并数据
# df1 和 df2 共同属性：name
# 和sql中join合并一样，用共同的列合并

pd.merge(df1,df2) # 相当于 inner join on a.name=b.name，默认how='inner'可省略不写
```

Out[80]:

|      |  name | height | weight |
| ---: | ----: | -----: | -----: |
|    0 | Peter |    189 |     70 |
|    1 |  Mike |    195 |     63 |
|    2 |   Ana |    178 |     75 |
|    3 |  John |    177 |     88 |

In [81]:

```python
pd.merge(df1,df2,how='outer') # 相当于 full outer join on a.name=b.name ，没有的位置填充空数据
```

Out[81]:

|      |  name | height | weight |
| ---: | ----: | -----: | -----: |
|    0 | Peter |  189.0 |   70.0 |
|    1 |  Mike |  195.0 |   63.0 |
|    2 |   Ana |  178.0 |   75.0 |
|    3 |  John |  177.0 |   88.0 |
|    4 |  张三 |  180.0 |    NaN |
|    5 |  李四 |    NaN |   67.0 |

In [82]:

```python
pd.merge(df1,df2,how='left') # left join 以df1为主表，空的填充空数据
```

Out[82]:

|      |  name | height | weight |
| ---: | ----: | -----: | -----: |
|    0 | Peter |    189 |   70.0 |
|    1 |  Mike |    195 |   63.0 |
|    2 |   Ana |    178 |   75.0 |
|    3 |  John |    177 |   88.0 |
|    4 |  张三 |    180 |    NaN |

In [83]:

```python
pd.merge(df1,df3,left_on='name',right_on='名字') # 列名不同，但是列的值形同
```

Out[83]:

|      |  name | height |  名字 | salary |
| ---: | ----: | -----: | ----: | -----: |
|    0 | Peter |    189 | Peter |     45 |
|    1 |  Mike |    195 |  Mike |     83 |
|    2 |   Ana |    178 |   Ana |     63 |
|    3 |  John |    177 |  John |     26 |
|    4 |  张三 |    180 |  张三 |     93 |

In [84]:

```python
score = pd.DataFrame(
    data=np.random.randint(0,151,size=(5,3)),
    index=list('ABCDE'),
    columns=['Python','Math','En']
)
score
```

Out[84]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |    132 |   87 |  137 |
|    B |      5 |   68 |   17 |
|    C |      7 |   13 |   62 |
|    D |      7 |  107 |  133 |
|    E |     63 |  144 |  125 |

In [98]:

```python
score_mean = score.mean(axis=1).round(1)
score_mean.name = '平均分'
score_mean
```

Out[98]:

```
A    118.7
B     30.0
C     27.3
D     82.3
E    110.7
Name: 平均分, dtype: float64
```

In [91]:

```python
# score和score_mean 列名不相同，列的值也不相同，只有行索引相同，因此利用 行索引相同合并
pd.merge(score,score_mean,left_index=True,right_index=True)
```

Out[91]:

|      | Python | Math |   En | 平均分 |
| ---: | -----: | ---: | ---: | -----: |
|    A |    132 |   87 |  137 |  118.7 |
|    B |      5 |   68 |   17 |   30.0 |
|    C |      7 |   13 |   62 |   27.3 |
|    D |      7 |  107 |  133 |   82.3 |
|    E |     63 |  144 |  125 |  110.7 |

# 数据清洗

```python
df = pd.DataFrame(
    data={
        'color':['red','blue','red','green','green','blue',None,np.NaN],
        'price':[20,15,20,18,18,22,30,30]
    }
)
df
```

Out[99]:

|      | color | price |
| ---: | ----: | ----: |
|    0 |   red |    20 |
|    1 |  blue |    15 |
|    2 |   red |    20 |
|    3 | green |    18 |
|    4 | green |    18 |
|    5 |  blue |    22 |
|    6 |  None |    30 |
|    7 |   NaN |    30 |

In [100]:

```python
# 删除重复数据
df.drop_duplicates() # NaN和None是一回事 都表示空数据 也是重复数据
# drop_duplicates(inplace=True) inplace参数默认False，即不改变全数组，返回去重后的copy，True即在原数组上去重，改变原数组
```

Out[100]:

|      | color | price |
| ---: | ----: | ----: |
|    0 |   red |    20 |
|    1 |  blue |    15 |
|    3 | green |    18 |
|    5 |  blue |    22 |
|    6 |  None |    30 |

In [101]:

```python
# 删除空数据
df.dropna() # None和NaN 都被删除
```

Out[101]:

|      | color | price |
| ---: | ----: | ----: |
|    0 |   red |    20 |
|    1 |  blue |    15 |
|    2 |   red |    20 |
|    3 | green |    18 |
|    4 | green |    18 |
|    5 |  blue |    22 |

In [102]:

```python
# 删除行
df.drop(labels=[2,4,6]) # 默认axis=0省略，0维方向删除，就是删除行
```

Out[102]:

|      | color | price |
| ---: | ----: | ----: |
|    0 |   red |    20 |
|    1 |  blue |    15 |
|    3 | green |    18 |
|    5 |  blue |    22 |
|    7 |   NaN |    30 |

In [103]:

```python
# 删除列
df.drop(labels='price',axis=1) # 指明axis=1，1维方向删除，就是删除列
```

Out[103]:

|      | color |
| ---: | ----: |
|    0 |   red |
|    1 |  blue |
|    2 |   red |
|    3 | green |
|    4 | green |
|    5 |  blue |
|    6 |  None |
|    7 |   NaN |

In [105]:

```python
# 过滤数据
df.filter(items=['price']) # 保留数据price
```

Out[105]:

|      | price |
| ---: | ----: |
|    0 |    20 |
|    1 |    15 |
|    2 |    20 |
|    3 |    18 |
|    4 |    18 |
|    5 |    22 |
|    6 |    30 |
|    7 |    30 |

In [ ]:

```python
Examples
--------
>>> df = pd.DataFrame(np.array(([1, 2, 3], [4, 5, 6])),
...                   index=['mouse', 'rabbit'],
...                   columns=['one', 'two', 'three'])
>>> df
        one  two  three
mouse     1    2      3
rabbit    4    5      6

>>> # select columns by name
>>> df.filter(items=['one', 'three'])
         one  three
mouse     1      3
rabbit    4      6

>>> # select columns by regular expression
>>> df.filter(regex='e$', axis=1)
         one  three
mouse     1      3
rabbit    4      6

>>> # select rows containing 'bbi'
>>> df.filter(like='bbi', axis=0)
         one  two  three
rabbit    4    5      6
```

In [119]:

```python
# 异常值过滤
a = np.random.randint(0,10,size=10)
cond = (a<8) & (a>2) # 规定大于8，小于2是异常值
a[cond]
```

Out[119]:

```
array([4, 6, 4])
```

```python
# 正态分布异常值，大于3sigma就是异常值
# 3sigma准则：数值分布在(μ-3σ,μ+3σ)中的概率为0.9974，μ是平均值，σ是标准差
```

```python
# 重置行索引，inplace=True表示在原数组上修改
jobs.reset_index(inplace=True)
```

# 数据转换

## 轴和元素转换

In [121]:

```python
df = pd.DataFrame(
    data=np.random.randint(0,151,size=(5,3)),
    index=list('ABCDE'),
    columns=['Python','Math','En']
)
df
```

Out[121]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |     17 |   20 |   56 |
|    B |    100 |    8 |   67 |
|    C |     57 |    9 |  122 |
|    D |    108 |   31 |   46 |
|    E |     89 |  106 |  126 |

In [122]:

```python
# 修改索引
df.rename(
    index={'A':'K','C':'P'}, # 修改行索引
    columns={'Math':'Java'}, # 修改列索引
    inplace=True # True修改原数组，默认False
)
```

Out[122]:

|      | Python | Java |   En |
| ---: | -----: | ---: | ---: |
|    K |     17 |   20 |   56 |
|    B |    100 |    8 |   67 |
|    P |     57 |    9 |  122 |
|    D |    108 |   31 |   46 |
|    E |     89 |  106 |  126 |

In [123]:

```python
# 修改元素
df.replace(8,88,inplace=True) # 所有8都改成了88, inplace=True修改原数组，默认False
```

Out[123]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |     17 |   20 |   56 |
|    B |    100 |   88 |   67 |
|    C |     57 |    9 |  122 |
|    D |    108 |   31 |   46 |
|    E |     89 |  106 |  126 |

```python
Examples
--------

**Scalar `to_replace` and `value`**

>>> s = pd.Series([0, 1, 2, 3, 4])
>>> s.replace(0, 5)
0    5
1    1
2    2
3    3
4    4
dtype: int64

>>> df = pd.DataFrame({'A': [0, 1, 2, 3, 4],
...                    'B': [5, 6, 7, 8, 9],
...                    'C': ['a', 'b', 'c', 'd', 'e']})
>>> df.replace(0, 5)
   A  B  C
0  5  5  a
1  1  6  b
2  2  7  c
3  3  8  d
4  4  9  e

**List-like `to_replace`**

>>> df.replace([0, 1, 2, 3], 4)
   A  B  C
0  4  5  a
1  4  6  b
2  4  7  c
3  4  8  d
4  4  9  e

>>> df.replace([0, 1, 2, 3], [4, 3, 2, 1])
   A  B  C
0  4  5  a
1  3  6  b
2  2  7  c
3  1  8  d
4  4  9  e

>>> s.replace([1, 2], method='bfill')
0    0
1    3
2    3
3    3
4    4
dtype: int64

**dict-like `to_replace`**

>>> df.replace({0: 10, 1: 100})
     A  B  C
0   10  5  a
1  100  6  b
2    2  7  c
3    3  8  d
4    4  9  e

>>> df.replace({'A': 0, 'B': 5}, 100)
     A    B  C
0  100  100  a
1    1    6  b
2    2    7  c
3    3    8  d
4    4    9  e

>>> df.replace({'A': {0: 100, 4: 400}})
     A  B  C
0  100  5  a
1    1  6  b
2    2  7  c
3    3  8  d
4  400  9  e

**Regular expression `to_replace`**

>>> df = pd.DataFrame({'A': ['bat', 'foo', 'bait'],
...                    'B': ['abc', 'bar', 'xyz']})
>>> df.replace(to_replace=r'^ba.$', value='new', regex=True)
      A    B
0   new  abc
1   foo  new
2  bait  xyz

>>> df.replace({'A': r'^ba.$'}, {'A': 'new'}, regex=True)
      A    B
0   new  abc
1   foo  bar
2  bait  xyz

>>> df.replace(regex=r'^ba.$', value='new')
      A    B
0   new  abc
1   foo  new
2  bait  xyz

>>> df.replace(regex={r'^ba.$': 'new', 'foo': 'xyz'})
      A    B
0   new  abc
1   xyz  new
2  bait  xyz

>>> df.replace(regex=[r'^ba.$', 'foo'], value='new')
      A    B
0   new  abc
1   new  new
2  bait  xyz

Compare the behavior of ``s.replace({'a': None})`` and
``s.replace('a', None)`` to understand the peculiarities
of the `to_replace` parameter:

>>> s = pd.Series([10, 'a', 'a', 'b', 'a'])

When one uses a dict as the `to_replace` value, it is like the
value(s) in the dict are equal to the `value` parameter.
``s.replace({'a': None})`` is equivalent to
``s.replace(to_replace={'a': None}, value=None, method=None)``:

>>> s.replace({'a': None})
0      10
1    None
2    None
3       b
4    None
dtype: object

When ``value=None`` and `to_replace` is a scalar, list or
tuple, `replace` uses the method parameter (default 'pad') to do the
replacement. So this is why the 'a' values are being replaced by 10
in rows 1 and 2 and 'b' in row 4 in this case.
The command ``s.replace('a', None)`` is actually equivalent to
``s.replace(to_replace='a', value=None, method='pad')``:

>>> s.replace('a', None)
0    10
1    10
2    10
3     b
4     b
dtype: object
```

## map映射元素转变

In [124]:

```python
df
```

Out[124]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |     17 |   20 |   56 |
|    B |    100 |    8 |   67 |
|    C |     57 |    9 |  122 |
|    D |    108 |   31 |   46 |
|    E |     89 |  106 |  126 |

In [127]:

```python
# map 只能针对一列，就是Series
# 根据字典对数据进行改变，不在字典中的数据就返回NaN
df['Python'].map({17:1717, 57:5757})
```

Out[127]:

```
A    1717.0
B       NaN
C    5757.0
D       NaN
E       NaN
Name: Python, dtype: float64
```

In [129]:

```python
df['Math'].map(lambda x: x > 10) # lambda表达式，Math列，大于10返回True，否则返回False
```

Out[129]:

```
A     True
B    False
C    False
D     True
E     True
Name: Math, dtype: bool
```

In [130]:

```python
def convert(x):
    if x > 60:
        return True
    else:
        return False

df['En'].map(convert) # 自定义函数，map将En列每一行值传给convert
```

Out[130]:

```
A    False
B     True
C     True
D    False
E     True
Name: En, dtype: bool
```

## apply映射元素转变

In [131]:

```python
# apply既可以操作Series，又可以操作DataFrame
df
```

Out[131]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |     17 |   20 |   56 |
|    B |    100 |    8 |   67 |
|    C |     57 |    9 |  122 |
|    D |    108 |   31 |   46 |
|    E |     89 |  106 |  126 |

In [132]:

```python
df.apply(lambda x: x+100)
```

Out[132]:

|      | Python | Math |   En |
| ---: | -----: | ---: | ---: |
|    A |    117 |  120 |  156 |
|    B |    200 |  108 |  167 |
|    C |    157 |  109 |  222 |
|    D |    208 |  131 |  146 |
|    E |    189 |  206 |  226 |

In [135]:

```python
def convert(x):
    return (x.min(),x.max(),x.mean(),x.std())

df.apply(convert) # 默认axis=0，对每一列进行聚合统计，指定axis=1，会对每一行进行聚合统计
```

Out[135]:

|      |     Python |       Math |         En |
| ---: | ---------: | ---------: | ---------: |
|    0 |  17.000000 |   8.000000 |  46.000000 |
|    1 | 108.000000 | 106.000000 | 126.000000 |
|    2 |  74.200000 |  34.800000 |  83.400000 |
|    3 |  37.399198 |  40.886428 |  37.825917 |

```python
Signature: df.apply(func, axis=0, raw=False, result_type=None, args=(), **kwds)
    
Examples
--------
>>> df = pd.DataFrame([[4, 9]] * 3, columns=['A', 'B'])
>>> df
   A  B
0  4  9
1  4  9
2  4  9

Using a numpy universal function (in this case the same as
``np.sqrt(df)``):

>>> df.apply(np.sqrt)
     A    B
0  2.0  3.0
1  2.0  3.0
2  2.0  3.0

Using a reducing function on either axis

>>> df.apply(np.sum, axis=0)
A    12
B    27
dtype: int64

>>> df.apply(np.sum, axis=1)
0    13
1    13
2    13
dtype: int64

Returning a list-like will result in a Series

>>> df.apply(lambda x: [1, 2], axis=1)
0    [1, 2]
1    [1, 2]
2    [1, 2]
dtype: object

Passing ``result_type='expand'`` will expand list-like results
to columns of a Dataframe

>>> df.apply(lambda x: [1, 2], axis=1, result_type='expand')
   0  1
0  1  2
1  1  2
2  1  2

Returning a Series inside the function is similar to passing
``result_type='expand'``. The resulting column names
will be the Series index.

>>> df.apply(lambda x: pd.Series([1, 2], index=['foo', 'bar']), axis=1)
   foo  bar
0    1    2
1    1    2
2    1    2

Passing ``result_type='broadcast'`` will ensure the same shape
result, whether list-like or scalar is returned by the function,
and broadcast it along the axis. The resulting column names will
be the originals.

>>> df.apply(lambda x: [1, 2], axis=1, result_type='broadcast')
   A  B
0  1  2
1  1  2
2  1  2
```

## transform元素转变

```python
df = pd.DataFrame(
    data=np.random.randint(0,10,size=(10,3)),
    index=list('ABCDEFGHIJ'),
    columns=['python','tensorflow','keras']
)
display(df)
# 可以针对Series进行运算
df['python'].transform(lambda x: 1024 if x > 5 else -1024) # 三目运算符 x if a>b else y。如果a>b返回x，否则y
```

|      | python | tensorflow | keras |
| ---: | -----: | ---------: | ----: |
|    A |      4 |          1 |     8 |
|    B |      8 |          7 |     7 |
|    C |      4 |          4 |     9 |
|    D |      1 |          6 |     4 |
|    E |      1 |          0 |     1 |
|    F |      7 |          8 |     7 |
|    G |      1 |          4 |     4 |
|    H |      7 |          3 |     4 |
|    I |      7 |          3 |     2 |
|    J |      7 |          6 |     1 |

Out[136]:

```
A   -1024
B    1024
C   -1024
D   -1024
E   -1024
F    1024
G   -1024
H    1024
I    1024
J    1024
Name: python, dtype: int64
```

In [137]:

```python
df['tensorflow'].transform([np.sqrt,np.square,np.cumsum]) # 针对一列进行不同的操作，apply也可以
```

Out[137]:

|      |     sqrt | square | cumsum |
| ---: | -------: | -----: | -----: |
|    A | 1.000000 |      1 |      1 |
|    B | 2.645751 |     49 |      8 |
|    C | 2.000000 |     16 |     12 |
|    D | 2.449490 |     36 |     18 |
|    E | 0.000000 |      0 |     18 |
|    F | 2.828427 |     64 |     26 |
|    G | 2.000000 |     16 |     30 |
|    H | 1.732051 |      9 |     33 |
|    I | 1.732051 |      9 |     36 |
|    J | 2.449490 |     36 |     42 |

In [138]:

```python
def convert(x):
    return True if x>5 else False

# 可以针对DataFrame进行运算
df.transform({'python':np.cumsum,'tensorflow':np.square,'keras':convert}) # 对不同的列，执行不同的操作，apply也可以
```

Out[138]:

|      | python | tensorflow | keras |
| ---: | -----: | ---------: | ----: |
|    A |      4 |          1 |  True |
|    B |     12 |         49 |  True |
|    C |     16 |         16 |  True |
|    D |     17 |         36 | False |
|    E |     18 |          0 | False |
|    F |     25 |         64 |  True |
|    G |     26 |         16 | False |
|    H |     33 |          9 | False |
|    I |     40 |          9 | False |
|    J |     47 |         36 | False |

## map，apply，applymap的区别

- apply：应用在DataFrame的行或列中，也可以应用到单独一个Series的每个元素中
- map：应用在单独一个Series的每个元素中
- applymap：应用在DataFrame的每个元素中

map：

map只能操作一个Series，应用在每个元素上

```python
df.map(...) # 报错
df['Python'].map(lambda x:int(x)) # 可运行，Python列每个元素数据类型被转为int
```

apply：

apply即可操作DataFrame，又可操作一个或多个Series

操作DataFrame时，应用到行或列上；操作Series时，应用到每个元素上

```python
df.apply(lambda x:int(x)) # 报错 cannot convert the series to <class 'int'>，不能把一个series类型转为int类型
df['Python'].apply(lambda x:int(x)) # 可运行，应用的是一个元素，lambda接收的是一个具体的数据
```

applymap：

applymap只能操作DataFrame，并且应用在每个元素上

```python
df['Python'].applymap(...) # 报错 'Series' object has no attribute 'applymap'
df.applymap(lambda x:int(x)) # 可运行
```

## 重排随机抽样哑变量

In [139]:

```python
df
```

Out[139]:

|      | python | tensorflow | keras |
| ---: | -----: | ---------: | ----: |
|    A |      4 |          1 |     8 |
|    B |      8 |          7 |     7 |
|    C |      4 |          4 |     9 |
|    D |      1 |          6 |     4 |
|    E |      1 |          0 |     1 |
|    F |      7 |          8 |     7 |
|    G |      1 |          4 |     4 |
|    H |      7 |          3 |     4 |
|    I |      7 |          3 |     2 |
|    J |      7 |          6 |     1 |

In [141]:

```python
index = np.random.permutation(10) # 0到10，打乱顺序
index
```

Out[141]:

```
array([3, 1, 2, 5, 9, 7, 6, 0, 4, 8])
```

In [142]:

```python
# 重排 索引打乱
df.take(index)
```

Out[142]:

|      | python | tensorflow | keras |
| ---: | -----: | ---------: | ----: |
|    D |      1 |          6 |     4 |
|    B |      8 |          7 |     7 |
|    C |      4 |          4 |     9 |
|    F |      7 |          8 |     7 |
|    J |      7 |          6 |     1 |
|    H |      7 |          3 |     4 |
|    G |      1 |          4 |     4 |
|    A |      4 |          1 |     8 |
|    E |      1 |          0 |     1 |
|    I |      7 |          3 |     2 |

In [144]:

```python
# 随机抽样10个数据
df.take(np.random.randint(0,10,size=10))
```

Out[144]:

|      | python | tensorflow | keras |
| ---: | -----: | ---------: | ----: |
|    A |      4 |          1 |     8 |
|    F |      7 |          8 |     7 |
|    D |      1 |          6 |     4 |
|    A |      4 |          1 |     8 |
|    G |      1 |          4 |     4 |
|    D |      1 |          6 |     4 |
|    D |      1 |          6 |     4 |
|    F |      7 |          8 |     7 |
|    B |      8 |          7 |     7 |
|    G |      1 |          4 |     4 |

In [145]:

```python
# 哑变量
df2 = pd.DataFrame(data={'key':['a','b','a','b','c','b','c']})
df2
```

Out[145]:

|      |  key |
| ---: | ---: |
|    0 |    a |
|    1 |    b |
|    2 |    a |
|    3 |    b |
|    4 |    c |
|    5 |    b |
|    6 |    c |

In [146]:

```python
# one-hot，哑变量
# str类型数据，经过哑变量转换，就可以使用数字表示
pd.get_dummies(df2,prefix='',prefix_sep='') # 1表示有，0表示没有
```

Out[146]:

|      |    a |    b |    c |
| ---: | ---: | ---: | ---: |
|    0 |    1 |    0 |    0 |
|    1 |    0 |    1 |    0 |
|    2 |    1 |    0 |    0 |
|    3 |    0 |    1 |    0 |
|    4 |    0 |    0 |    1 |
|    5 |    0 |    1 |    0 |
|    6 |    0 |    0 |    1 |

# 数据重塑

```python
df
```

Out[147]:

|      | python | tensorflow | keras |
| ---: | :----- | :--------- | :---- |
|    A | 4      | 1          | 8     |
|    B | 8      | 7          | 7     |
|    C | 4      | 4          | 9     |
|    D | 1      | 6          | 4     |
|    E | 1      | 0          | 1     |
|    F | 7      | 8          | 7     |
|    G | 1      | 4          | 4     |
|    H | 7      | 3          | 4     |
|    I | 7      | 3          | 2     |
|    J | 7      | 6          | 1     |

In [148]:

```python
df.T # 行列转置
```

Out[148]:

|            | A    | B    | C    | D    | E    | F    | G    | H    | I    | J    |
| ---------: | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
|     python | 4    | 8    | 4    | 1    | 1    | 7    | 1    | 7    | 7    | 7    |
| tensorflow | 1    | 7    | 4    | 6    | 0    | 8    | 4    | 3    | 3    | 6    |
|      keras | 8    | 7    | 9    | 4    | 1    | 7    | 4    | 4    | 2    | 1    |

In [149]:

```python
df2 = pd.DataFrame(
    data=np.random.randint(0,151,size=(6,3)),
    columns=['Python','Math','En'],
    index=pd.MultiIndex.from_product([list('ABC'),['期中','期末']]) # 多层索引
)
df2
```

Out[149]:

|      |      | Python | Math | En   |
| ---: | ---: | :----- | :--- | :--- |
|    A | 期中 | 6      | 29   | 87   |
| 期末 |   70 | 65     | 71   |      |
|    B | 期中 | 97     | 144  | 40   |
| 期末 |  115 | 5      | 32   |      |
|    C | 期中 | 7      | 79   | 30   |
| 期末 |   37 | 14     | 129  |      |

In [150]:

```python
df2.unstack(level=-1) # 将行索引，变成列索引，level默认-1，表示最后一层
```

Out[150]:

|      | Python | Math | En   |      |      |      |
| ---: | :----- | :--- | :--- | :--- | :--- | :--- |
|      | 期中   | 期末 | 期中 | 期末 | 期中 | 期末 |
|    A | 6      | 70   | 29   | 65   | 87   | 71   |
|    B | 97     | 115  | 144  | 5    | 40   | 32   |
|    C | 7      | 37   | 79   | 14   | 30   | 129  |

In [152]:

```python
df2.unstack(level=1)
```

Out[152]:

|      | Python | Math | En   |      |      |      |
| ---: | :----- | :--- | :--- | :--- | :--- | :--- |
|      | 期中   | 期末 | 期中 | 期末 | 期中 | 期末 |
|    A | 6      | 70   | 29   | 65   | 87   | 71   |
|    B | 97     | 115  | 144  | 5    | 40   | 32   |
|    C | 7      | 37   | 79   | 14   | 30   | 129  |

In [153]:

```python
df2.stack() # 列变成行
```

Out[153]:

```
A  期中  Python      6
       Math       29
       En         87
   期末  Python     70
       Math       65
       En         71
B  期中  Python     97
       Math      144
       En         40
   期末  Python    115
       Math        5
       En         32
C  期中  Python      7
       Math       79
       En         30
   期末  Python     37
       Math       14
       En        129
dtype: int64
```

In [155]:

```python
df2.unstack().stack(level=0)
```

Out[155]:

|        |      | 期中 | 期末 |
| -----: | ---: | :--- | :--- |
|      A |   En | 87   | 71   |
|   Math |   29 | 65   |      |
| Python |    6 | 70   |      |
|      B |   En | 40   | 32   |
|   Math |  144 | 5    |      |
| Python |   97 | 115  |      |
|      C |   En | 30   | 129  |
|   Math |   79 | 14   |      |
| Python |    7 | 37   |      |

In [156]:

```
df2
```

Out[156]:

|      |      | Python | Math | En   |
| ---: | ---: | :----- | :--- | :--- |
|    A | 期中 | 6      | 29   | 87   |
| 期末 |   70 | 65     | 71   |      |
|    B | 期中 | 97     | 144  | 40   |
| 期末 |  115 | 5      | 32   |      |
|    C | 期中 | 7      | 79   | 30   |
| 期末 |   37 | 14     | 129  |      |

In [157]:

```
df2.mean() # 计算每一列的平均值
```

Out[157]:

```
Python    55.333333
Math      56.000000
En        64.833333
dtype: float64
```

In [158]:

```
df2.mean(axis=1) # 计算每一行的平均值
```

Out[158]:

```
A  期中    40.666667
   期末    68.666667
B  期中    93.666667
   期末    50.666667
C  期中    38.666667
   期末    60.000000
dtype: float64
```

In [159]:

```
df2.mean(level=0)
```

Out[159]:

|      | Python | Math | En   |
| ---: | :----- | :--- | :--- |
|    A | 38.0   | 47.0 | 79.0 |
|    B | 106.0  | 74.5 | 36.0 |
|    C | 22.0   | 46.5 | 79.5 |

In [163]:

```
df2.mean(level=1)
```

Out[163]:

|      | Python    | Math | En        |
| ---: | :-------- | :--- | :-------- |
| 期中 | 36.666667 | 84.0 | 52.333333 |
| 期末 | 74.000000 | 28.0 | 77.333333 |