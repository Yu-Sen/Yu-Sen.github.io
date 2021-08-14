---
title: 【AI人工智能】机器学习-有监督学习
date: 2021-07-29 19:31:47
tags:
- 机器学习
- 有监督学习
- KNN
categories:
- 学习笔记
description: 有监督学习-KNN模型、决策树模型、线性回归模型
---

# 有监督学习

有监督学习是对数据若干==特征==和若干==标签==之间的关联性进行建模的过程。

有监督学习就是先得到一个模型，再去进行预测。

这类学习任务可以进一步分为==分类任务==和==回归任务==。

分类任务，标签都是离散的。

回归任务，标签都是连续的。

下面介绍几种有监督学习的模型：

分类问题：KNN模型、决策树

回归问题：线性回归

# KNN/K近邻模型（KNearestNeighbors）

KNN模型是有监督学习算法模型中的一种，同时也是最简单的一种。

**原理**：通过距离判断两个样本是否相似，如果距离够近就认为他们相似是同一类别。

实际中不是找出距离最近的1个点，而是多个点，点的个数为k，k人为设定。

找出距离最近的k个点后，看这些点都属于哪一类，哪类点最多，就认为新样本属于这一类。

**距离计算**：两个点的坐标，对应维度值差的平方，然后全部加起来后开放。

- 二维向量：$d=\sqrt[]{(x_A-x_B)^2+(y_A-y_B)^2}$
- n维向量：$d=\sqrt[]{(x_{A1}-x_{B1})^2+(x_{A2}-x_{B2})^2+...+(x_{An}-x_{Bn})^2}$

**实现过程**：假设 X_test 待标记的数据样本，X_train 为已标记的数据集。

- 遍历已标记数据集中所有的样本，计算每个样本与待标记点的距离，并把距离保存在 Distance 数 组中。
- 对 Distance 数组进行排序，**取距离最近的** **k** **个点，记为** **X_knn**。
- 在 **X_knn** 中**统计每个类别的个数**，即 class0 在 X_knn 中有几个样本，class1 在 X_knn 中有几个 样本等。
- 待标记样本的类别，就是在 X_knn 中样本个数最多的那个类别。

**算法优缺点**：

- k是人为设定的，k过大或者过小都会不准确。

机器学习中，这种需要人为设定的参数称为==超参数==。

可以通过==学习曲线==来找最优的k，这时的k值预测的准确率是最高的。

**算法变种**：

- 当k设为4，恰好4个点分别属于2类，这就无法分辨待标记点属于哪个类别。这种情况可以指定权重，比如这4个距离最近的点，距离待标记点的距离越近权重越高。就是在4个最近的点中再比较哪个点距离更近。
- 使用一定半径内的点，取代距离最近的k个点。数据采样不均匀时，该算法变种可以取得更好的性能。

## python代码实现knn算法案例

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# 解决坐标轴刻度负号乱码
plt.rcParams['axes.unicode_minus'] = False
# 解决中文乱码问题
plt.rcParams['font.sans-serif'] = ['Hiragino Sans GB']
plt.style.use('ggplot')

# 要预测的数据
new_data = np.array([12.03, 4.1])
# K值
K = 3
# 数据集
rowdata = {
    '颜色深度': [14.13, 13.2, 13.16, 14.27, 13.24, 12.07, 12.43, 11.79, 12.37, 12.04],
    '酒精浓度': [5.64, 4.28, 5.68, 4.80, 4.22, 2.76, 3.94, 3.1, 2.12, 2.6],
    '品种': [0, 0, 0, 0, 0, 1, 1, 1, 1, 1]}
# 0 代表 “黑皮诺”，1 代表 “赤霞珠”
wine_data = pd.DataFrame(rowdata)
wine_data
```

|      | 颜色深度 | 酒精浓度 | 品种 |
| ---: | -------: | -------: | ---: |
|    0 |    14.13 |     5.64 |    0 |
|    1 |    13.20 |     4.28 |    0 |
|    2 |    13.16 |     5.68 |    0 |
|    3 |    14.27 |     4.80 |    0 |
|    4 |    13.24 |     4.22 |    0 |
|    5 |    12.07 |     2.76 |    1 |
|    6 |    12.43 |     3.94 |    1 |
|    7 |    11.79 |     3.10 |    1 |
|    8 |    12.37 |     2.12 |    1 |
|    9 |    12.04 |     2.60 |    1 |

```python
# 一般将特征和标签提取出来单独处理。特征一般赋给变量X，标签一般赋给变量y
# 计算待标记点与所有样本的距离
wine_data['距离'] = np.sqrt((wine_data['颜色深度'] - new_data[0]) ** 2 +
                          (wine_data['酒精浓度'] - new_data[1]) ** 2)
wine_data
```

|      | 颜色深度 | 酒精浓度 | 品种 |     距离 |
| ---: | -------: | -------: | ---: | -------: |
|    0 |    14.13 |     5.64 |    0 | 2.604151 |
|    1 |    13.20 |     4.28 |    0 | 1.183765 |
|    2 |    13.16 |     5.68 |    0 | 1.942498 |
|    3 |    14.27 |     4.80 |    0 | 2.346828 |
|    4 |    13.24 |     4.22 |    0 | 1.215936 |
|    5 |    12.07 |     2.76 |    1 | 1.340597 |
|    6 |    12.43 |     3.94 |    1 | 0.430813 |
|    7 |    11.79 |     3.10 |    1 | 1.028397 |
|    8 |    12.37 |     2.12 |    1 | 2.008980 |
|    9 |    12.04 |     2.60 |    1 | 1.500033 |

```python
# 按距离升序排序
wine_data.sort_values(by='距离', inplace=True)
wine_data
```

|      | 颜色深度 | 酒精浓度 | 品种 |     距离 |
| ---: | -------: | -------: | ---: | -------: |
|    6 |    12.43 |     3.94 |    1 | 0.430813 |
|    7 |    11.79 |     3.10 |    1 | 1.028397 |
|    1 |    13.20 |     4.28 |    0 | 1.183765 |
|    4 |    13.24 |     4.22 |    0 | 1.215936 |
|    5 |    12.07 |     2.76 |    1 | 1.340597 |
|    9 |    12.04 |     2.60 |    1 | 1.500033 |
|    2 |    13.16 |     5.68 |    0 | 1.942498 |
|    8 |    12.37 |     2.12 |    1 | 2.008980 |
|    3 |    14.27 |     4.80 |    0 | 2.346828 |
|    0 |    14.13 |     5.64 |    0 | 2.604151 |

```python
# 显示距离最近的K个样本
wine_data.head(n=K)
```

|      | 颜色深度 | 酒精浓度 | 品种 |     距离 |
| ---: | -------: | -------: | ---: | -------: |
|    6 |    12.43 |     3.94 |    1 | 0.430813 |
|    7 |    11.79 |     3.10 |    1 | 1.028397 |
|    1 |    13.20 |     4.28 |    0 | 1.183765 |

```python
# 统计距离最近的K个样本品种频数,返回出现最多的品种
wine_data.head(n=K)['品种'].value_counts().idxmax()
# 1


# 画散点图
plt.figure(figsize=(9, 6))
plt.scatter(x=wine_data[wine_data['品种']==0]['颜色深度'],
            y=wine_data[wine_data['品种']==0]['酒精浓度'],
            color='purple',label='黑皮诺')
plt.scatter(x=wine_data[wine_data['品种']==1]['颜色深度'],
            y=wine_data[wine_data['品种']==1]['酒精浓度'],
            color='red',label='赤霞珠')
plt.scatter(x=new_data[0], y=new_data[1], color='yellow')
plt.xlabel(xlabel='颜色深度')
plt.ylabel(ylabel='酒精浓度')
plt.legend(loc='lower right')
```

![img](data:image/png;base64,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)



## sklearn算法库实现knn算法案例

scikit-learn，简称sklearn，是python中最重要的机器学习库。支持包括**分类、回归、聚类、降维**四大机器学习算法，以及 **特征提取、数据预处理、模型评估**三大模块。

```python
# sklearn算法库实现knn算法
from sklearn.neighbors import KNeighborsClassifier

# knn分类器，n_neighbors就是k值，默认5
clf = KNeighborsClassifier(n_neighbors=3)

# fit训练数据，参数为特征X和标签y
clf = clf.fit(X=wine_data.iloc[:,:2],y=wine_data.iloc[:,2])

# 造一批测试集数据
a = np.random.normal(11,2,(10,1))
b = np.random.normal(5,1,(10,1))
test_data = np.concatenate([a,b],axis=1)
test_data
'''
array([[ 7.90819839,  5.44747629],
       [10.28049007,  4.03974467],
       [15.3449895 ,  4.15660601],
       [11.35887358,  4.12086594],
       [ 8.86405246,  6.57394667],
       [ 9.66772374,  4.21797473],
       [13.10532154,  6.92509304],
       [ 8.56989513,  5.78155508],
       [ 7.48573085,  6.1910778 ],
       [11.42802847,  6.25444522]])
'''

# predict预测，返回测试集的预测结果
result = clf.predict(test_data)
result
'array([1, 1, 0, 1, 1, 1, 0, 1, 1, 0])'

# 假如测试数据的真实结果如下
test_realResult = np.array([0, 1, 0, 1, 1, 1, 0, 1, 1, 0])
# score对模型进行评估，参数为测试集数据和测试数据的真实结果，返回预测准确率
score = clf.score(test_data,test_realResult)
score
'0.9'

# predict_proba预测每个样本是某个标签的概率。左边一列是标签为0的概率，右边一列是标签为1的概率。
clf.predict_proba(test_data)
'''
array([[0.        , 1.        ],
       [0.        , 1.        ],
       [1.        , 0.        ],
       [0.        , 1.        ],
       [0.33333333, 0.66666667],
       [0.        , 1.        ],
       [1.        , 0.        ],
       [0.33333333, 0.66666667],
       [0.33333333, 0.66666667],
       [0.66666667, 0.33333333]])
'''
```

## train_test_split划分训练集和测试集

一般将数据集的80%抽取出来作为训练集，其余20%作为测试集。

训练集train的特征一般命名为Xtrain，标签命名为Ytrain。

测试集test的特征一般命名为Xtest，标签命名为Ytest。

sklearn.model_selection模块提供了train_test_split方法来从数据集中划分训练集和测试集。

```python
from sklearn.neighbors import KNeighborsClassifier
# sklearn.datasets是sklearn提供的数据集demo，导入乳腺癌breast_cancer这个demo的数据集
from sklearn.datasets import load_breast_cancer
# sklearn.model_selection的train_test_split方法用来划分训练集和测试集
from sklearn.model_selection import train_test_split
import pandas as pd
import numpy as np  

#读取数据集
data = load_breast_cancer()
#DateFrame格式显示
X = data.data
y = data.target
name = ['平均半径', '平均纹理', '平均周长', '平均面积',
        '平均光滑度', '平均紧凑度', '平均凹度', '平均凹点',
        '平均对称', '平均分形维数', '半径误差', '纹理误差',
        '周长误差', '面积误差', '平滑度误差', '紧凑度误差', '凹度误差',
        '凹点误差', '对称误差', '分形维数误差', '最差半径',
        '最差纹理', '最差的边界', '最差的区域', '最差的平滑度',
        '最差的紧凑性', '最差的凹陷', '最差的凹点', '最差的对称性',
        '最差的分形维数', '患病否']
data = np.concatenate((X, y.reshape(-1, 1)), axis=1)
table = pd.DataFrame(data=data, columns=name)
table.head()
```

|      | 平均半径 | 平均纹理 | 平均周长 | 平均面积 | 平均光滑度 | 平均紧凑度 | 平均凹度 | 平均凹点 | 平均对称 | 平均分形维数 |  ... | 最差纹理 | 最差的边界 | 最差的区域 | 最差的平滑度 | 最差的紧凑性 | 最差的凹陷 | 最差的凹点 | 最差的对称性 | 最差的分形维数 | 患病否 |
| ---: | -------: | -------: | -------: | -------: | ---------: | ---------: | -------: | -------: | -------: | -----------: | ---: | -------: | ---------: | ---------: | -----------: | -----------: | ---------: | ---------: | -----------: | -------------: | -----: |
|    0 |    17.99 |    10.38 |   122.80 |   1001.0 |    0.11840 |    0.27760 |   0.3001 |  0.14710 |   0.2419 |      0.07871 |  ... |    17.33 |     184.60 |     2019.0 |       0.1622 |       0.6656 |     0.7119 |     0.2654 |       0.4601 |        0.11890 |    0.0 |
|    1 |    20.57 |    17.77 |   132.90 |   1326.0 |    0.08474 |    0.07864 |   0.0869 |  0.07017 |   0.1812 |      0.05667 |  ... |    23.41 |     158.80 |     1956.0 |       0.1238 |       0.1866 |     0.2416 |     0.1860 |       0.2750 |        0.08902 |    0.0 |
|    2 |    19.69 |    21.25 |   130.00 |   1203.0 |    0.10960 |    0.15990 |   0.1974 |  0.12790 |   0.2069 |      0.05999 |  ... |    25.53 |     152.50 |     1709.0 |       0.1444 |       0.4245 |     0.4504 |     0.2430 |       0.3613 |        0.08758 |    0.0 |
|    3 |    11.42 |    20.38 |    77.58 |    386.1 |    0.14250 |    0.28390 |   0.2414 |  0.10520 |   0.2597 |      0.09744 |  ... |    26.50 |      98.87 |      567.7 |       0.2098 |       0.8663 |     0.6869 |     0.2575 |       0.6638 |        0.17300 |    0.0 |
|    4 |    20.29 |    14.34 |   135.10 |   1297.0 |    0.10030 |    0.13280 |   0.1980 |  0.10430 |   0.1809 |      0.05883 |  ... |    16.67 |     152.20 |     1575.0 |       0.1374 |       0.2050 |     0.4000 |     0.1625 |       0.2364 |        0.07678 |    0.0 |

```python
# train_test_split划分训练集和测试集
# 参数：X数据集的特征，y数据集的标签，test_size=0.2表示将数据集的20%作为测试集
# random_state 随机数种子：其实就是该组随机数的编号，在需要重复试验的时候，保证得到一组一样的随机数。
# 比如你每次都填1，其他参数一样的情况下你得到的随机数组是一样的。但填0或不填，每次都会不一样。
# 返回值：X_train,X_test,y_train,y_test
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=420)


# 使用train训练集建立knn模型训练
clf = KNeighborsClassifier(n_neighbors=4)
clf = clf.fit(X_train,y_train)


# 使用test测试集预测，评估模型
clf.predict(X_test)
clf.score(X_test,y_test)
'0.9210526315789473'
```

## 绘制学习曲线选择最优K值

上面案例中，K设置为4时，准确率92%，那么K=4就是最优K值吗？K设为其他值时准确率如何？

```python
clf = KNeighborsClassifier(n_neighbors=3)
clf.score(X_test,y_test)
'0.9298245614035088'

clf = KNeighborsClassifier(n_neighbors=4)
clf.score(X_test,y_test)
'0.9210526315789473'

clf = KNeighborsClassifier(n_neighbors=5)
clf.score(X_test,y_test)
'0.9385964912280702'
```

可见，K的取不同，直接影响最终模型的准确度。

==在机器学习中，这种不同取值对模型预测有直接影响的参数，叫做超参数==。

那么K应该设置为多少，预测才是最准的呢？

我们把每一个K值得到的预测准确率，绘制成一个折线图，从图中看出哪个K值的准确率最高。这个折线图就叫做学习曲线。

```python
# 绘制学习曲线，找到最优K值
krange = range(1,20)
score = []

for i in krange:
    clf = KNeighborsClassifier(n_neighbors=i)
    clf = clf.fit(X_train,y_train)
    score.append(clf.score(X_test,y_test))

plt.plot(krange,score)
plt.xticks(range(1,20))
```

![img](data:image/png;base64,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)

折线图中可以看出，当K=8和12的时候，预测的准确率是最高的。

那么此时的K值就是最优解吗？这个案例中，我们只划分了一次训练集和测试集，有没有可能刚好这一次的测试集只对当前的训练集表现良好？是有这种可能的。

## 交叉验证，K折交叉验证

为了尽可能消除这种影响，就需要用到交叉验证。

==对数据集划分多次，得到多个评分，求出多个评分的平均分和方差。然后再在多个K值下进行这个操作，每个K值就对应一个平均分和方差，绘制成曲线就是带交叉验证的学习曲线。==

数据集划分K次，也叫K折。

==折数和K值同样，不能设置过大，也不能设置过小。K值可以通过学习曲线来找最优K，折数一般设置5或6折，这是经验数据。==

K折交叉验证是交叉验证中最简单最常用的方式。

原理就是数据集的80%作为训练集，在训练集中再划分出65%作为训练集用来训练模型，剩余35%作为验证集用来找K值。

**带交叉验证的学习曲线：**

```python
# 带交叉验证的学习曲线

# 取出特征数据X和标签数据y
X = table.iloc[:,:-1]
y = table.iloc[:,-1]

# 划分训练集和测试集
from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=420)

# 每个K值下，使用交叉验证训练数据得到评分均值和方差
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import cross_val_score

mean = []
var = []
for i in range(1,20):
    KNN = KNeighborsClassifier(n_neighbors=i)
    # cross_val_score k折交叉验证函数
    # 参数：estimator 模型对象，X 特征数据，y 标签数据，cv 折数，就是进行几次划分
    # 返回：评分列表，每次划分得出的评分组成
    result = cross_val_score(estimator=KNN,X=X_train,y=y_train,cv=5)
    mean.append(result.mean())
    var.append(result.var())

mean = np.array(mean)
var = np.array(var)

# 绘制出交叉验证得到的学习曲线
plt.plot(range(1,20),mean,color='k')
plt.plot(range(1,20),mean+var*2,color='red',linestyle='--')
plt.plot(range(1,20),mean-var*2,color='red',linestyle='--')
plt.xticks(range(1,20))
```

![img](data:image/png;base64,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)

当K=8时，评分的均值最高，且方差较小，表现最出色。因此8就是最优K值。

因为同一算法模型在不同的训练集和测试集的会得到不同的准确率，无法调参。

所以在sklearn 中可以通过添加random_state，通过固定random_state的值，每次可以分割得到同样训练集和测试集。

因此random_state参数主要是为了保证每次都分割一样的训练集和测试机，大小可以是任意一个整数，在调参环节，只要保证其值一致即可。

## 归一化

当数据集中，有的特征数据值非常大，比如10000，而有的特征数据值相比很小，比如0.1，那么在knn模型计算距离的时候，值很小的特征对距离的影响就微不足道了，因为0.1对10000的影响太小了。

这种现象称作==量纲不统一==，一个是万级的，一个是小数级的。

这种情况就需要对特征数据进行归一化处理，将所有数据压缩到同一个范围内。

==统一量纲的计算公式==：
$$
x=\frac{x-min()}{max()-min()}
$$
消除量纲后的数据 =（当前数据➖这组数据的最小值）➗（这组数据的最大值➖这组数据的最小值）

**sklearn的MinMaxScaler函数实现归一化：**

```python
# 划分训练集和测试集
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=420)

# 归一化
from sklearn.preprocessing import MinMaxScaler
mm = MinMaxScaler(feature_range=(0,1)) # 数据缩放范围，默认缩放至0-1之间
X_train = mm.fit_transform(X_train)
X_test = mm.fit_transform(X_test)

# 训练集和测试集的特征数据都已进行了归一化处理
# 接下来可以进行k折交叉验证绘制学习曲线了
mean = []
var = []
for i in range(1,20):
    KNN = KNeighborsClassifier(n_neighbors=i)
    result = cross_val_score(KNN,X_train,y_train,cv=5)
    mean.append(result.mean())
    var.append(result.var())

mean = np.array(mean)
var = np.array(var)

# 绘制出交叉验证得到的学习曲线
_ = plt.plot(range(1,20),mean,color='k')
_ = plt.plot(range(1,20),mean+var*2,color='red',linestyle='--')
_ = plt.plot(range(1,20),mean-var*2,color='red',linestyle='--')
_ = plt.xticks(range(1,20))
```

![img](data:image/png;base64,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)

## 标准化

归一化根据公式可以看出，某组特征数据中的最大值和最小值是影响归一化的因素，如果这组特征数据的最大值或最小值恰好是异常值，那么这种情况下进行归一化，一定会对归一化后的特征数据产生不好的影响。

这种情况就需要使用标准化，来统一数据量纲。

标准化计算公式：
$$
x=\frac{x-mean}{\sigma}
$$
某组特征数据，（每个数据➖这组数据的平均值）➗ 这组数据的标准差

sklearn同样提供了StandarScaler方法，来标准化特征数据。

```python
from sklearn.preprocessing import StandardScaler
ss = StandardScaler()
X_train = ss.fit_transform(X_train)
X_test = ss.fit_transform(X_test)
```

## 距离惩罚

就是之前说过的权重。

假设K取4，得到4个距离最近的点，刚好属于2个类别，此时无法通过投票来判断目标点属于哪个类别。

这是，将距离作为权重，距离越近的点权重越高，看哪个类别的点权重高，目标点就属于哪个类别。

==当训练集的点分布很不均匀，通过计算距离较难判断分类的时候，可以给模型设置距离权重。==

==KNeighborsClassifier(n_neighbors=i,weights='distance')中传入weights='distance'参数即可。==

```python
from sklearn.model_selection import train_test_split
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=420)

from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import cross_val_score

mean = []
var = []
for i in range(1,20):
    KNN = KNeighborsClassifier(n_neighbors=i,weights='distance')
    result = cross_val_score(KNN,X_train,y_train,cv=5)
    mean.append(result.mean())
    var.append(result.var())

mean = np.array(mean)
var = np.array(var)

_ = plt.plot(range(1,20),mean,color='k')
_ = plt.plot(range(1,20),mean+var*2,color='red',linestyle='--')
_ = plt.plot(range(1,20),mean-var*2,color='red',linestyle='--')
_ = plt.xticks(range(1,20))
```

![img](data:image/png;base64,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)

# 决策树模型（DecisionTree）

树算法模型是有监督学习算法模型中应用最广泛的一类算法模型。

==决策树模型能够从一系列有特征和标签的数据中总结出决策规则，并用树状图的结构来呈现这些规则，以解决分类问题和回归问题==。

解决分类问题的树模型称为分类树，解决回归问题的树模型称为回归树。

**原理：**树模型通过递归切割的方式来寻找最佳分类标准。

决策树算法本质是一种树结构，我们只需要问一系列问题就可以对数据进行分类。

比如有如下数据集，我们希望通过决策树模型来对数据进行分类，看看某个数据属于哺乳动物类别还是非哺乳动物类别：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt69mhp9p0j30q40au3zo.jpg)

我们根据数据集得出了如下一个决策树模型来对数据进行分类预测：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt69wovl07j30j40dydg6.jpg)

- 根结点：上图中【体温】这个特征的节点就是根结点，根结点是这个决策树的开始，根结点只有出边没有入边
- 内部节点（或非叶节点）：【胎生】这个特征的节点是内部节点，有1条入边和多条出边
- 叶节点：【哺乳动物】【非哺乳动物】这些标签数据就是叶节点，叶节点是这个决策树的最终分类结果。叶节点只有入边，没有出边

有了这个决策树模型，我们就可以对数据进行分类预测了，比如我现在有一个新的数据，放入到这个模型中，先看这个数据的体温特征是恒温还是冷血，如果是恒温再看胎生特征，最终得出这个数据是哺乳动物还是非哺乳动物。

==这里要提出问题了，这个决策树是怎么得到的？凭什么体温这个特征就是根结点呢？如果让我来构建决策树，我该用哪个特征作为根结点，哪些特征作为内部节点？所有特征都要作为节点放到决策树中吗？还是只用选取几个特征作为节点即可，该如何选取呢？==

## 纯度、熵（也叫信息熵、香浓熵）、信息增益、基尼系数

在回答如何构建决策树之前，先要了解几个概念：

==熵==，这个概念是物理、化学中的概念。

==熵，是对某个物质或系统内部混乱程度的度量==。

==熵值==越高，该物质或系统就越混乱，该物质或系统就越==不纯==。

==熵值==越低，该物质或系统就越稳定，该物质或系统就越==纯==。

那么在决策树中，熵值有什么意义呢？

==在信息论与概率统计中，熵是表示随机变量不确定性的度量==。

假如有A、B两个集合，他们各自都包含了一些元素：

- A = [1, 2, 3, 1, 4, 2, 3]
- B = [1, 1, ,1, 1, 1, 2, 1]

可以看出来

A集合中各种元素都有，且没有哪个元素占的特别多，没有明显的倾向性，我们就说A集合的==纯度==低。

而B集合中元素1占的很多，倾向性很明显，我们就说B集合的==纯度==高。

就像高中化学里，我们学过的纯净物和混合物一样。

==熵值==就是度量纯度的一个值，它是可以通过公式计算出的数值。

**熵值如何计算？**

熵值计算公式：$熵值=-\sum_{i=1}^nP_ilog_2(P_i)$，Pi就是集合中每个元素在该集合中出现的概率，概率=该元素出现的次数/集合总共的元素个数。

比如集合A=[1,2,3,4]，集合B=[1,1,1,1]：

集合A中，有1234四个元素，每个元素出现的概率都是1/4，所以集合A的熵值$=-(\frac{1}{4}*log_2\frac{1}{4}+\frac{1}{4}*log_2\frac{1}{4}+\frac{1}{4}*log_2\frac{1}{4}+\frac{1}{4}*log_2\frac{1}{4})=2$

集合B中，4个元素都是1，每个元素出现的概率是1，集合B的熵值$=-(1*log_21+1*log_21+1*log_21+1*log_21)=0$

集合A内部的类别很复杂，集合A内部更混乱，集合A的熵值也更高；集合B内部的类别很单一，内部更纯净，集合B的熵值也更低。

==基尼系数==和熵值的作用一样，也是衡量集合的纯度的一个值，只不过计算方式不同。

基尼系数计算公式：$1-\sum_{i=1}^np_i^2$，和熵值一样，内部越混乱，每个元素出现的概率就相对越小，得出的基尼系数就越大。

## 构建决策树的基本思想

构建决策树的基本思想是：

- ==随着树深度的增加，节点的熵值要迅速降低，降低的速度越快越好。==
- ==树的高度越矮越好，能用3步完成分类，就不用5步。==

节点熵值要迅速降低，回答了根结点选择哪个特征，内部节点选择哪些特征，哪些特征在前哪些在后的问题。这一步叫做==特征选择==。

树的高度越矮越好，回答了是否需要用所有特征来构建决策树的问题。这一步叫做==决策树的剪枝==。

## 特征选择——哪个特征做根结点，哪些特征做内部节点？

来看这个案例：

假设有以下数据集，数据集有14条数据，有4个特征outlook, temperature, humidity, windy表示天气的状况，1个标签play表示这一天我是否出去玩。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt6y0djxvjj31em0u0jtj.jpg)

现在要通过这份历史数据，来预测我今天会不会出去玩。

==首先，我们直接用历史数据结果的概率分布来判断新数据的结果：==

历史数据总共14条，其中play=yes的有9条，play=no的有5条。所以结合历史数据，今天我出去玩的概率就是9/14，不会出去玩的概率是5/14。

那么用这样的方式来预测今天play=多少，靠不靠谱呢？

我们可以计算一下这种方式的熵值，也就是原始数据集结果集合的熵值：$-(\frac{9}{14}*log_2(\frac{9}{14})+\frac{5}{14}*log_2(\frac{5}{14}))=0.940$

可以看到，**原始数据的结果的熵值是很高的，也就是说原始数据结果的倾向性并不明显，直接通过原始数据结果的概率分布来进行预测，是不准确的。**

==那么，我们再尝试通过历史数据的特征值对应的结果概率分布来进行预测：==

既然直接用结果也就是标签的概率分布来预测并不准确，那么我们想到用特征来进行预测。

比如我们拿outlook这个特征来进行预测。outlook这个特征有三种值：sunny、overcast、rainy。每种值下又对应着多个结果。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt73bq6ebej31em0u0jub.jpg)

整理一下：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt73i8iwcbj30g80dw74h.jpg)

outlook这个特征，有三种值sunny，overcast，rainy。

值是sunny的数据有5条，其中结果是yes的2个，结果是no的3个。

值是overcast的数据有4条，其中结果是yes的4个，结果是no的0个。

值是rainy的数据有5条，其中结果是yes的3个，结果是no的2个。

那么假如我们要对一条新的数据预测他的play是yes还是no，就可以通过新数据的outlook特征值来预测结果play。

根据历史数据，当值是sunny时，play=yes的概率是2/5，play=no的概率是3/5

当值是overcast时，play=yes的概率是4/4，play=no的概率是0/4

当值是rainy是也一样，能计算出yes和no的概率。

假如新数据的outlook特征的值是sunny，那么预测结果play是yes的概率就是2/5，是no的概率就是3/5

**那么这种做法看起来和直接用结果来统计预测好像差不多，只不过是去按特征值对应结果的概率分布去预测的。这种方式和直接用结果预测哪个更好呢？**

我们可以计算出这种方式的熵值，和直接用结果预测的熵值来比较，哪个熵值更低，不就说明哪个方式更好吗。

这种方式，也就是outlook这个特征的数据集合的熵值是如何计算的呢？

outlook有三种特征值，每种特征值又包含了多个最终的结果，因此我们可以把每种特征值的熵值计算出来。

每种值的数据又占这个特征的数据集的一部分，那么我们对每种特征值的熵值进行一个加权平均，就得出了outlook这个特征的熵值。

加权平均的意思就是：比如用综合成绩考核一名学生。综合成绩中，平时成绩占20%，期中成绩占40%，期末成绩占40%。那这名学生的综合成绩就=20%\*平时成绩+40%\*期中成绩+40%\*期末成绩。这就是加权平均。期中20%，40%，40%是各项的权重。

回到案例中

sunny的熵值：$-(\frac{2}{5}*log_2(\frac{2}{5})+\frac{3}{5}*log_2(\frac{3}{5}))=0.9709505944546686$

overcast的熵值：$-(\frac{4}{4}*log_2(\frac{4}{4})+\frac{0}{4}*log_2(\frac{0}{4}))=0$

rainy的熵值：$-(\frac{3}{5}*log_2(\frac{3}{5})+\frac{2}{5}*log_2(\frac{2}{5}))=0.9709505944546686$

sunny值有5条占5/14，overcast值有4条占4/14，rainy值有5条占5/14。

outlook特征集合的熵值：$\frac{5}{14}*sunny的熵值+\frac{4}{14}*overcast的熵值+\frac{5}{14}*rainy的熵值=0.6935361388961918$

得出outlook特征集合的熵值为0.69，比原数据结果集合的熵值0.94要低。

**所以说outlook特征集合的倾向性更明显，用outlook特征数据来判断最终结果，更有判断性，更能做出清晰准确的判断。**

----

经过刚才的计算，我们知道了用特征进行决策判断，比直接用结果进行决策判断更好。

那应该用哪个特征作为决策树的开始，作为根结点呢？

我们计算出了数据集中outlook这个特征的熵值，当然我们也可以用同样的方式，计算出temperature、humidity、windy这是三个特征的熵值。

经过计算我们得出：

- outlook熵值：0.693
- temperature熵值：0.910
- humidity熵值：0.788
- windy熵值：0.892

可见，outlook的熵值是最低的，它是最有利于我们做出清晰判断的，所以我们选择outlook作为根结点。如果从一开始我们都不能做出清晰决策，那后面的决策就都是在错误的基础上进行的。所以我们计算出所有特征的熵值，选择熵值最小的特征作为根结点——决策的开始。

这里出现一个概念：==信息增益==。

信息增益就是：当前节点父节点的熵值 ➖ 当前节点的熵值

比如上面的例子中，我们是在选择哪个特征作为根结点，根结点的父节点就是全体数据集。全体数据集的熵值就是标签列或者叫结果集合的熵值。

==如果当前节点是一个内部节点，那他的父节点就他上面的节点的所有样本，而不是全体数据，这点需要注意。==

所以根结点outlook的信息增益就是：父节点全体数据的熵值0.940➖当前节点根结点outlook的熵值0.693 = 0.247，计作gain(outlook)=0.247

同样也可以计算出其他特征的信息增益。

可见，特征的熵值越低，它的信息增益就越高。两个正好成相反的关系。

我们在选择特征做为节点的时候，往往也通过这个特征在这个节点上的信息增益大小来选择，信息增益最大的就是我们的选择。

我们来看看outlook作为根结点是如何进行决策的：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt76595lsvj30sc0bagmi.jpg)

outlook特征有3种值，当值是overcast时，根据数据集我们可以直接判定结果play=yes，因为数据集中overcast有4条数据，且结果全部是yes。

当值时sunny或rainy时，在数据集中他们的结果有yes也有no，并且这两个值的熵值都很高，无法直接清晰判断出结果。

所以我们也可以把sunny/rainy放到一起，整理一下：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt76cfhtroj30vu0d6wfj.jpg)

我们经过对数据集各特征的熵值计算，选择出熵值最低的特征outlook作为根结点。

当新数据的outlook特征为overcast时，直接得出结果play=yes。

当新数据的outlook特征为sunny或rainy时，无法得出结果，进入到下一个子节点，也就是内部节点进行下一层决策判断。

---

下一个节点，内部节点要用哪个特征？

其实和选择根结点时是一样的。只不过这里要注意：

在根结点outlook这层决策中，当outlook=overcast时直可以接得出结果，所以数据集中outlook=overcast的4条数据不需要再进入下一层决策判断了，剩下10条数据，这层决策无法做出清晰判断，就作为下一层决策的数据集继续进行决策判断：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt77rlmjkcj31bc0ryad7.jpg)

![](https://tva1.sinaimg.cn/large/008i3skNgy1gt77tdx3wkj30vk0d8wfm.jpg)

现在，下一个节点的数据确定了，接下来就是重复选择根结点时的计算，计算剩下10条数据，他们的temperature、humidity、windy这三个特征的熵值，选择出熵值最小或者信息增益最大的特征作为这个内部节点的特征。

这个内部节点选择出来之后，再进行这一层的决策，有无法清晰判断的特征值数据，再进行一下层节点的决策判断。

这样，每个节点选择特征时，都通过计算熵值、信息增益，来选择最优的特征，这样就达到了局部最优。

==每个节点都达到局部最优，最终整个决策树就逐渐逼近全局最优。==

**每层都重复同样的计算逻辑，这也就解释了最开始介绍决策树的原理：通过递归切割的方式寻找最佳分类标准。**

## 决策树剪枝——决策树拟合度优化

上面特征选择的案例中，数据集只有4个特征，针对这个数据集来构建生成一个决策树，为了每个节点决策尽可能清晰准确，每个节点我都不停分割，直到不能再分，能直分出最终结果为止，最终4个特征都作为节点存在于决策树中。

假如数据集有4000个、4万个特征呢？难道我们也要从根结点开始，为了每个节点决策都能清晰准确，而不停的分割节点直到能直接分出结果吗，这显然是不对的。

如果决策树分的过于详细过于庞大，数据集中的每个样本最终都能走到一个熵值是0的叶节点，那就会出现过拟合的情况。

就想KNN算法一样，如果为了追求精确找到最近邻居而把K设置为1，同样会出现过拟合。

这是就需要对决策树的生成做出一些限制，不让它分的太过详细、深度过深，这就是决策树的剪枝。

同样除了过拟合的风险，如果节点太少也会出现欠拟合的风险。

==构建决策树的每个节点时，考虑的是当前节点的局部最优。决策树的剪枝，考虑是全局最优。==

**预剪枝**：在决策树生成的过程中，对每个节点在**划分前先进行估计**，如果当前的节点划分不能带来决策树泛化性能(预测性能)的提升，则停止划分并且将当前节点标记为叶节点。

**后剪枝**：先训练生成一颗完整的树，**自底向上**对非叶节点进行考察，如果将该节点对应的子树替换为叶 节点能带来决策树泛化能力的提升，则将该子树替换为叶节点。

实际中使用后剪枝为主，相比预剪枝，后剪枝有训练开销小，泛化能力强、欠拟合风险小的优点。

## ID3算法

上面的案例中，其实就是ID3算法的实现。

ID3算法是由J. Ross Quinlan于1975年在悉尼大学提出的一种分类预测算法。

ID3算法就是通过计算信息增益来确定节点的划分。

**ID3算法的缺陷：**

1 

假如数据集中有一个id列，从1一直递增到14。现在id列也作为特征，参与到节点特征选取。

那么id列每一个值都是一类值，每一类值都只有一个样本结果。因此每一类值的熵值都是0，最终id列的熵值也是0，id列的信息增益是最大的。

按照ID3算法，就应该选取id特征为最优特征。

这显然是不对的，这就是ID3算法的缺陷，如果数据集中某些特征的值的类别很多，并且每个类别包含的样本又很少，就会出现按照信息增益来选取最优特征不准确的情况。

## C4.5算法

C4.5算法就是在ID3算法上的改进，通过计算信息增益率而不是信息增益来选择最优特征。

**信息增益率如何计算：**
$$
信息增益率=\frac{该特征的信息增益}{该特征列自身的熵值}
$$
⚠️注意：分母是该特征列自身的熵值，而不是之前选择最优特征时，计算的特征列对应样本结果的熵值。

什么意思呢？比如上面说的id列，直接计算信息增益，id列的信息增益是最大的。

如果计算id列的信息增益率，分母就是id列这个集合的熵值，等于$-(\frac{1}{14}log_2\frac{1}{14}+\frac{1}{14}log_2\frac{1}{14}+...+\frac{1}{14}log_2\frac{1}{14})=3.807354922057604$

由于id列的值种类非常多，所以这列的纯度很低，熵值就非常大，分母非常大，得出的信息增益率就会很小了，即使分子信息增益很大。

用信息增益率代替信息增益，就能避免选到哪些值的类别很多，并且每个类别包含的样本又很少的特征了。

## CART算法

Classification and Regression Tree 分类回归树算法

相比ID3，C4.5，CART算法有以下特点：

- CART生成的树必须是二叉树，内部节点只能根据特征值进行二分
- 预测变量既可以是连续变量，也可以是分类变量
- 使用Gini系数作为指标选择节点特征。

**cart算法剪枝：**

通过训练集训练出模型，用验证集测试，得出如下结果：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtc2c8jdk0j30nu0cot9b.jpg)

判断每个叶节点在验证集上的错误率:

- 节点 4 的错误率 e(4)=1/3
- 节点 5 的错误率 e(5)=1
- 节点 6 的错误率 e(6)=1
- 节点 7 的错误率为e(7)=4/9

计算子节点总加权平均错误率并和父节点进行比较，加权方法就是乘以该节点样本量占父节点样本总量 的百分比(验证集)。

如节点 2 的错误率为e(2)=1/4 ，而节点 4 和节点 5 的加权平均错误率为e(4)\*3/4+e(5)\*1/4=2/4 ，因此子节点错误率更高，考虑剪枝;

节点 3 的错误率 e(3)=3/10，而节点6和节点 7 的加权平均错误率为e(6)\*1/10+e(7)\*9/10=4/10 ，子节点错误率更高，因此考虑剪枝;

节点 2 和节点 3 的加权平均错误率为e(2)\*4/14+e(3)\* 10/14=4/14，比父节点(节点 1)的错误率2/14要大，因此考虑剪枝。

## python实现ID3算法

**全部数据熵值计算：**

```python
# 熵值计算
row_data = {'是否陪伴': [0, 0, 0, 1, 1],
            '是否玩游戏': [1, 1, 0, 1, 1],
            '渣男': ['是', '是', '不是', '不是', '不是']}
dataSet = pd.DataFrame(row_data)
dataSet
```

|      | 是否陪伴 | 是否玩游戏 | 渣男 |
| ---: | -------: | ---------: | ---: |
|    0 |        0 |          1 |   是 |
|    1 |        0 |          1 |   是 |
|    2 |        0 |          0 | 不是 |
|    3 |        1 |          1 | 不是 |
|    4 |        1 |          1 | 不是 |

```python
def calEnt(dataSet):
    n = dataSet.shape[0] # 总共有多少数据
    m = dataSet.iloc[:,-1].value_counts() # 标签列各类别值的频数
    ent = -(m/n*np.log2(m/n)).sum() # 熵值计算
    return ent
calEnt(dataSet)

0.9709505944546686
```

**【是否陪伴】特征列信息增益计算：**

```python
# 计算第0列特征的信息增益
def calGain(dataSet):
    # 计算父节点熵值
    p_ent = calEnt(dataSet)
    # 计算子节点熵值
    c_m = dataSet.iloc[:,0].value_counts() # 第0列各种值的频数
    # 计算该列各种值的熵值
    lst = []
    for i in c_m.index:
        cond = dataSet.iloc[:,0]==i
        display(dataSet[cond])
        display(calEnt(dataSet[cond]))
        lst.append(calEnt(dataSet[cond]))
    # 各种值的熵值加权平均，计算出第0列的熵值
    c_ent = (c_m/dataSet.shape[0]*lst).sum()
    display(c_ent)

    # 第0列信息增益
    return (p_ent - c_ent)
calGain(dataSet)
```

|      | 是否陪伴 | 是否玩游戏 | 渣男 |
| ---: | -------: | ---------: | ---: |
|    0 |        0 |          1 |   是 |
|    1 |        0 |          1 |   是 |
|    2 |        0 |          0 | 不是 |

```python
# 这类值的熵值
0.9182958340544896
```

|      | 是否陪伴 | 是否玩游戏 | 渣男 |
| ---: | -------: | ---------: | ---: |
|    3 |        1 |          1 | 不是 |
|    4 |        1 |          1 | 不是 |

```python
# 这类值的熵值
0.0
```

```python
# 第0列的熵值
0.5509775004326937
# 第0列的信息增益
0.4199730940219749
```

**计算每一列的信息增益，选出最优特征：**

```python
def calEnt(dataSet):
    n = dataSet.shape[0] # 总共有多少数据
    m = dataSet.iloc[:,-1].value_counts() # 标签列各类别值的频数
    p = m/n # 各类别值出现的概率
    ent = (-p*np.log2(p)).sum() # 熵值计算
    return ent
  
# 计算每一列的信息增益，选出最优特征
def bestSplit(dataSet):
    bestGain = 0
    bestColumn=''
    # 计算父节点熵值
    p_ent = calEnt(dataSet)
    # 遍历每一特征列
    for column in dataSet.iloc[:,:-1].columns:
        # 计算子节点熵值
        c_m = dataSet[column].value_counts() # 该列各种值的频数
        # 计算该列各种值的熵值
        lst = []
        for i in c_m.index:
            cond = dataSet[column]==i
            lst.append(calEnt(dataSet[cond]))
        # 各种值的熵值加权平均，计算该列的熵值和信息增益
        c_ent = (c_m/dataSet.shape[0]*lst).sum()
        gain = p_ent-c_ent
        # 比较该列信息增益和最优信息增益，大的为新的最优信息增益
        if gain > bestGain:
            bestGain=gain
            bestColumn=column
        bestGain = p_ent-c_ent
        # 返回该列信息增益
        print('【%s】列的熵值：%f' % (column,c_ent))
        print('【%s】列的信息增益：%f' % (column,p_ent-c_ent))
    print('最优特征是：%s' % bestColumn)
bestSplit(dataSet)
```

```
【是否陪伴】列的熵值：0.550978
【是否陪伴】列的信息增益：0.419973
【是否玩游戏】列的熵值：0.800000
【是否玩游戏】列的信息增益：0.170951
最优特征是：是否陪伴
```

**生成完整的决策树：**

```python
# 计算熵值
def calEnt(dataSet):
    n = dataSet.shape[0]  # 总共有多少数据
    m = dataSet.iloc[:, -1].value_counts()  # 标签列各类别值的频数
    p = m / n  # 各类别值出现的概率
    ent = (-p * np.log2(p)).sum()  # 熵值计算
    return ent

# 生成节点
# 传入的数据集，首次是全部数据，之后应该是子节点对应的数据集
def createNode(dataSet):
    bestGain = 0
    bestFeature = ''
    childNode = []
    # 当前数据集的熵值，就是父节点熵值
    p_ent = calEnt(dataSet)
    # 遍历当前数据集每一特征列，计算每一特征熵值，找出当前节点最优特征
    for colName in dataSet.columns[:-1]:
        # 该特征列每种值的频数
        c_m = dataSet[colName].value_counts()
        # 计算该特征列每种值的熵值
        lst = []
        for i in c_m.index:
            cond = dataSet[colName] == i
            lst.append(calEnt(dataSet[cond]))
        # 各种值的熵值加权平均，计算该列的熵值和信息增益
        c_ent = (c_m / dataSet.shape[0] * lst).sum()
        gain = p_ent - c_ent
        # 比较该列信息增益和最优信息增益，大的为新的最优信息增益
        if gain > bestGain:
            bestGain = gain
            bestFeature = colName
            childNode = []
            # 判断当前特征列各类数据，如果某类值的熵为0，则直接作为叶节点，如果不为0，这类值的数据集作为子节点的数据集
            for i in c_m.index:
                cond = dataSet[colName] == i
                if calEnt(dataSet[cond])==0:
                    childNode.append({
                        'sample':dataSet[cond].shape[0],
                        'value':i,
                        'result':'{}{}'.format(dataSet[cond].iloc[:,-1].values[0],dataSet.columns[-1]),
                        'isLeafNode': True
                    })
                else:
                    childNode.append({
                        'sample':dataSet[cond].shape[0],
                        'value':i,
                        'isLeafNode':False,
                        'childDataSet':dataSet[cond].drop(labels=colName,axis=1)
                    })

        bestGain = p_ent - c_ent
    # 返回该节点最优特征列
    return (bestFeature,childNode)

# 递归生成树
def createTree(dataSet):
    node,childNodes = createNode(dataSet)
    myTree = {node:{}}
    for childNode in childNodes:
        if not childNode['isLeafNode']:
            myTree[node][childNode['value']] = createTree(childNode['childDataSet'])
        else:
            myTree[node][childNode['value']] = childNode['result']
    return myTree
createTree(dataSet)

{'是否陪伴': {
   0: {
     '是否玩游戏': {
       		1: '是渣男', 
          0: '不是渣男'}}, 
   1: '不是渣男'}
}
```

## sklearn实现分类树

### 导入包

```python
#全部行都能输出
from IPython.core.interactiveshell import InteractiveShell

InteractiveShell.ast_node_interactivity = "all"

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

# 导入数据集
from sklearn.datasets import load_wine
from sklearn.model_selection import train_test_split

# 解决坐标轴刻度负号乱码
plt.rcParams['axes.unicode_minus'] = False
# 解决中文乱码问题
plt.rcParams['font.sans-serif'] = ['Hiragino Sans GB']
```

### 数据预处理

```python
# 数据预处理
wine = load_wine()
# load_wine()返回字典结构的数据，data：特征数据，是一个二维数组。target：标签数据，是一个一维数组
# 通过np.concatenate方法，将两个数组合并，target.reshape将一维数组转成只有一列的二维数组
data = np.concatenate((wine.data,wine.target.reshape(-1,1)),axis=1)
column_names = ['酒精','苹果酸','灰','灰的碱性','镁','总酚','类黄酮','非黄烷类酚类','花青 素','颜色强度','色调','od280/od315 稀释葡萄酒','脯氨酸','标签']
wine_df = pd.DataFrame(data=data,columns=column_names)
wine_df
```

|      |  酒精 | 苹果酸 |   灰 | 灰的碱性 |    镁 | 总酚 | 类黄酮 | 非黄烷类酚类 | 花青 素 | 颜色强度 | 色调 | od280/od315 稀释葡萄酒 | 脯氨酸 | 标签 |
| ---: | ----: | -----: | ---: | -------: | ----: | ---: | -----: | -----------: | ------: | -------: | ---: | ---------------------: | -----: | ---: |
|    0 | 14.23 |   1.71 | 2.43 |     15.6 | 127.0 | 2.80 |   3.06 |         0.28 |    2.29 |     5.64 | 1.04 |                   3.92 | 1065.0 |  0.0 |
|    1 | 13.20 |   1.78 | 2.14 |     11.2 | 100.0 | 2.65 |   2.76 |         0.26 |    1.28 |     4.38 | 1.05 |                   3.40 | 1050.0 |  0.0 |
|    2 | 13.16 |   2.36 | 2.67 |     18.6 | 101.0 | 2.80 |   3.24 |         0.30 |    2.81 |     5.68 | 1.03 |                   3.17 | 1185.0 |  0.0 |
|    3 | 14.37 |   1.95 | 2.50 |     16.8 | 113.0 | 3.85 |   3.49 |         0.24 |    2.18 |     7.80 | 0.86 |                   3.45 | 1480.0 |  0.0 |
|    4 | 13.24 |   2.59 | 2.87 |     21.0 | 118.0 | 2.80 |   2.69 |         0.39 |    1.82 |     4.32 | 1.04 |                   2.93 |  735.0 |  0.0 |
|  ... |   ... |    ... |  ... |      ... |   ... |  ... |    ... |          ... |     ... |      ... |  ... |                    ... |    ... |  ... |
|  173 | 13.71 |   5.65 | 2.45 |     20.5 |  95.0 | 1.68 |   0.61 |         0.52 |    1.06 |     7.70 | 0.64 |                   1.74 |  740.0 |  2.0 |
|  174 | 13.40 |   3.91 | 2.48 |     23.0 | 102.0 | 1.80 |   0.75 |         0.43 |    1.41 |     7.30 | 0.70 |                   1.56 |  750.0 |  2.0 |
|  175 | 13.27 |   4.28 | 2.26 |     20.0 | 120.0 | 1.59 |   0.69 |         0.43 |    1.35 |    10.20 | 0.59 |                   1.56 |  835.0 |  2.0 |
|  176 | 13.17 |   2.59 | 2.37 |     20.0 | 120.0 | 1.65 |   0.68 |         0.53 |    1.46 |     9.30 | 0.60 |                   1.62 |  840.0 |  2.0 |
|  177 | 14.13 |   4.10 | 2.74 |     24.5 |  96.0 | 2.05 |   0.76 |         0.56 |    1.35 |     9.20 | 0.61 |                   1.60 |  560.0 |  2.0 |

178 rows × 14 columns

### 划分数据集

```python
# 划分数据集
x_train,x_test,y_train,y_test = train_test_split(wine_df.iloc[:,:-1],
                 wine_df.iloc[:,-1],
                 test_size=0.3, # 使用70%作为训练集
                 random_state=420)
x_train
```

|      |  酒精 | 苹果酸 |   灰 | 灰的碱性 |    镁 | 总酚 | 类黄酮 | 非黄烷类酚类 | 花青 素 | 颜色强度 | 色调 | od280/od315 稀释葡萄酒 | 脯氨酸 |
| ---: | ----: | -----: | ---: | -------: | ----: | ---: | -----: | -----------: | ------: | -------: | ---: | ---------------------: | -----: |
|  110 | 11.46 |   3.74 | 1.82 |     19.5 | 107.0 | 3.18 |   2.58 |         0.24 |    3.58 |     2.90 | 0.75 |                   2.81 |  562.0 |
|  161 | 13.69 |   3.26 | 2.54 |     20.0 | 107.0 | 1.83 |   0.56 |         0.50 |    0.80 |     5.88 | 0.96 |                   1.82 |  680.0 |
|   97 | 12.29 |   1.41 | 1.98 |     16.0 |  85.0 | 2.55 |   2.50 |         0.29 |    1.77 |     2.90 | 1.23 |                   2.74 |  428.0 |
|   81 | 12.72 |   1.81 | 2.20 |     18.8 |  86.0 | 2.20 |   2.53 |         0.26 |    1.77 |     3.90 | 1.16 |                   3.14 |  714.0 |
|  157 | 12.45 |   3.03 | 2.64 |     27.0 |  97.0 | 1.90 |   0.58 |         0.63 |    1.14 |     7.50 | 0.67 |                   1.73 |  880.0 |
|  ... |   ... |    ... |  ... |      ... |   ... |  ... |    ... |          ... |     ... |      ... |  ... |                    ... |    ... |
|   31 | 13.58 |   1.66 | 2.36 |     19.1 | 106.0 | 2.86 |   3.19 |         0.22 |    1.95 |     6.90 | 1.09 |                   2.88 | 1515.0 |
|   63 | 12.37 |   1.13 | 2.16 |     19.0 |  87.0 | 3.50 |   3.10 |         0.19 |    1.87 |     4.45 | 1.22 |                   2.87 |  420.0 |
|  134 | 12.51 |   1.24 | 2.25 |     17.5 |  85.0 | 2.00 |   0.58 |         0.60 |    1.25 |     5.45 | 0.75 |                   1.51 |  650.0 |
|   72 | 13.49 |   1.66 | 2.24 |     24.0 |  87.0 | 1.88 |   1.84 |         0.27 |    1.03 |     3.74 | 0.98 |                   2.78 |  472.0 |
|  177 | 14.13 |   4.10 | 2.74 |     24.5 |  96.0 | 2.05 |   0.76 |         0.56 |    1.35 |     9.20 | 0.61 |                   1.60 |  560.0 |

124 rows × 13 columns

### 训练决策树模型

```python
# 决策树模型建模
# 使用sklearn.tree中的DecisionTreeClassifier类
from sklearn.tree import DecisionTreeClassifier

# criterion参数：是设置使用gini基尼系数，还是entropy熵值作为节点选取特征的指标
clf = DecisionTreeClassifier(criterion='gini')
clf = clf.fit(x_train,y_train)
clf.score(x_test,y_test)

0.9444444444444444
```

### 生成可视化决策树

使用graphviz软件绘制决策树图片。graphviz是一款开源的图形可视化软件。

需要现在电脑中暗爽graphviz，其次在python中pip install graphviz，才可以在python环境中通过函数调用来使用graphviz绘图。

```python
# 决策树可视化
# 导入python的graphviz模块
import graphviz
# 导入sklearn的tree模块
from sklearn import tree

# 先通过tree.export_graphviz方法生成dot格式的决策树
# 参数：
# clf，模型
# out_file，输出dot文件的名字，默认为None表示不生成文件
# feature_names，所有特征的名称
# class_names，类标的名称，比如标签列有3种值[1,2,3]，class_namesclass_names=["琴酒","雪莉","贝尔摩德"],["琴酒","雪莉","贝尔摩德"]就对应[1,2,3]
# filled，是否给每个节点的主分类绘制不同的颜色，默认False
# rounded，是否给节点边框加圆角，默认True
dot_data = tree.export_graphviz(
    clf,
    out_file=None,
    feature_names=column_names[:-1],
    class_names=["琴酒","雪莉","贝尔摩德"],
    filled=True,
    rounded=True
)

# 在通过graphviz.Source()方法生成图片
graph = graphviz.Source(dot_data,filename='决策树.pdf')
graph

# render方法将生成的决策树保存成pdf
graph.render('wine')
# wine.pdf
```

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtd0n4e6qlj61000u0q6w02.jpg)

### 决策树模型的一些重要属性

**clf.feature_impotances_：每个特征对生成决策树的重要性**

这个属性是最重要的，用于剪枝。重要性很低的一些特征就可以考虑剪枝了。

```python
clf.feature_importances_
# zip()压缩函数，*zip()解压函数
[*zip(column_names,clf.feature_importances_)]

[('酒精', 0.0),
 ('苹果酸', 0.02195224501537985),
 ('灰', 0.0),
 ('灰的碱性', 0.0),
 ('镁', 0.0),
 ('总酚', 0.0),
 ('类黄酮', 0.3955768217709794),
 ('非黄烷类酚类', 0.0),
 ('花青 素', 0.0),
 ('颜色强度', 0.39720941385141617),
 ('色调', 0.024046373651715403),
 ('od280/od315 稀释葡萄酒', 0.0),
 ('脯氨酸', 0.16121514571050916)]

# python中的zip()压缩函数、*zip()解压函数
# zip() 函数：用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的列表，即元组列表
a = [1,2,3]
b = [4,5,6]
zip(a,b)：将a和b中的元素对应组合成元组。结果：(1,4),(2,5),(3,6)。而要想看到这个结果，需用*zip()函数
# *zip(a,b)：先将a和b中的元素对应组合成元组形成结果，再将这个结果解压，即 * 表示解压。但不输出，需借助print打印出来：
print(*zip(a,b))
(1,4) (2,5) (3,6)
```

**clf.apply(x_test)：返回每个测试样本所在的叶节点的索引**

```python
# 返回每个测试样本所在的叶节点的索引
clf.apply(x_test)

array([ 6,  2, 12, 12,  3, 12,  2, 12, 12,  2,  2,  6, 12,  2, 12,  6,  2,
        2, 12,  2,  2,  2, 12,  6, 12,  2,  6,  6,  2, 12, 12,  6,  2, 12,
        6, 12, 12, 10,  2, 12,  6,  2, 12,  6, 12, 12,  2,  6,  2,  6,  2,
       12, 12, 12])
```

**clf.tree_.node_count：返回树的节点个数**

```python
# 树的节点个数
clf.tree_.node_count

13
```

**clf.tree_.feature：每个节点对应的属性索引值，-2表示叶节点**

```python
# 每个节点对应的特征在数据中的索引值，-2表示叶节点
clf.tree_.feature

array([ 9, 12, -2, -2,  6, 10, -2, -2, 12,  1, -2, -2, -2], dtype=int64)
```

### 过拟合优化

在不加限制的情况下，一棵决策树会生长到衡量不纯度的指标最优，或者没有更多的特征可用为止。这样的决策树往往会过拟合。

当一棵决策树对训练数据有了过于优秀的解释性，它找出的规则必然包含了训练样本中的噪声，并使它对未知数据的拟合程度不足。

为了让决策树有更好的泛化性，我们要对决策树进行剪枝。**剪枝策略对决策树的影响巨大，正确的剪枝策略是优化决策树算法的核心。**

这里防止过拟合有两种方式：

方式一：

- random_state

- splitter：特征划分标准。有两个值best和random，best在特征的所有划分点中找出最优的划分点，random随机的在部分划分点中找局部最优的划分点。默认的‘best’适合样本量不大的时候，而如果样本数据量非常大，此时决策树构建推荐‘random’。

- 剪枝参数：

  - max_depth：限制树的最大深度，根结点不算，超过设定深度的树枝全部剪掉。**实际使用时，建议从=3 开始尝试，看看拟合的效果再决定是否增加设定深度**。

  - min_samples_leaf：一个节点在分支后的每个子节点都必须包含至少 min_samples_leaf 个训练样本，否则分支就不会发生， 或者，分支会朝着满足每个子节点都包min_samples_leaf 个样本的方向去发生。

    值为整数时，表示该节点的子节点至少包含多少个样本，比如min_samples_leaf=30，表示子节点至少包含30个样本

    值为浮点数时，表示该节点的子节点至少包含百分之多少个样本，比如min_samples_leaf=0.5，表示子节点至少包含父节点50%的样本。

  - min_samples_split：一个节点必须要包含至少 min_samples_split 个训练样本，这个节点才允许被分支，否则分支就不会发 生。就是说这个节点自身包含了至少min_samples_split个样本，并且不纯度没有达到0，才会继续往下切分，否则就不切分。

  - max_features：限制分支时考虑的特征个数，超过限制个数的特征都会被舍弃。 

  - min_impurity_decrease：限制信息增益的大小，信息增益小于设定数值的分支不会发生。

单个参数确定最优值依然使用学习曲线，多个参数最优组合使用网格搜索。

## 样本不均匀问题

样本不均衡是指在一组数据集中，标签的一类天生占有很大的比例，但我们有着捕捉出某种特定的分类 的需求的状况。比如，我们现在要对潜在犯罪者和普通人进行分类，潜在犯罪者占总人口的比例是相当 低的，也许只有2%左右，98%的人都是普通人，而我们的目标是要捕获出潜在犯罪者。这样的标签分布 会带来许多问题。

首先，分类模型天生会倾向于多数的类，让多数类更容易被判断正确，少数类被牺牲掉。

其次，模型评估指标会失去意义。这种分类状况下，即便模型什么也不做，全把所有人都当成不会犯罪 的人，准确率也能非常高。

解决样本不均匀带来的问题，有以下2种方式：

### class_weight参数

DecisionTreeClassifier()中，参数 class_weight 默认值 None，即自动认 为标签的比例是 1:1。所以当样本不均衡的时候，我们可以使用形如{标签值1：权重，标签值2：权重2}的字典来输入真实的样本标签比例，来让算法意识到样本是不平衡的。或者使用'balanced'。

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import make_blobs # 聚类产生数据集的方法

# 生成一份样本不均匀的数据集
class1 = 1000 # 类别1 1000个样本
class2 = 100  # 类别2 100个样本
centers = [[0,0],[2,2]] # 两个类别的中心点
clusters_std = [2.5,0.5] # 两个类别的方差

X,y = make_blobs(n_samples=[class1,class2],
           centers=centers,
           cluster_std = clusters_std,
           random_state=420,shuffle=False)
plt.scatter(X[:,0],X[:,1],c=y,cmap='rainbow',s=10)
```

![img](data:image/png;base64,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)

```python
# 划分数据集
x_train,x_test,y_train,y_test = train_test_split(X,y,test_size=0.2,random_state=420)
# 不设置class_weight
clf1 = DecisionTreeClassifier()
clf1.fit(x_train,y_train)
clf1.score(x_test,y_test)

0.8954545454545455

# 设置class_weight
clf2 = DecisionTreeClassifier(class_weight='balanced')
clf2.fit(x_train,y_train)
clf2.score(x_test,y_test)

0.9090909090909091
```

### 混淆矩阵

如果我们的目标是希望尽量捕获少数类，那准确率这个模型评估指标逐渐失效，所以我们需要新的模型评估指标来帮助我们。

简单来看，其实我们只需要查看模型在少数类上的准确率就好了，只要能够将少数类尽量捕捉出来，就能够达到我们的目的。

但此时，新问题又出现了，单纯地追求捕捉出少数类，就会成本太高，而不顾及少数类，又会无法达成模型的效果。

**如果一个模型在能够尽量捕获少数类的情况下，还能够尽量对多数类判断正确，则这个模型就非常优秀了**。为了评估这样的能力，我们将引入新的模型评估指标:混淆矩阵来帮助我们。

**混淆矩阵**是二分类问题的多维衡量指标体系，在样本不平衡时极其有用。

 在混淆矩阵中，我们将少数类认为是正例，多数类认为是负例。 

在决策树，随机森林这些分类算法里，即是说**少数类是 1，多数类是0**。

在 SVM 里，就是说**少数类是 1，多数类是-1**。

假如，现在有一个分类器A，它能够识别图片是不是汉堡。如果我向分类器中输入了9张图片，我们可以人工观察出分类的情况。但实际中，可能会传入成千上万张头片，这时就无法人工观察出分类的情况了。

混淆矩阵，就可以将分类结果简单、直观的展示出来。

图片实际的真实类别有2类：是汉堡、不是汉堡。分类器A预测的类别也是2类：是汉堡、不是汉堡。

我们将真实类别作为列，预测类别作为行，形成一个2X2的矩阵，这个矩阵就叫混淆矩阵。本案例中只有2个类别，所以是2X2，如果是3种类别，那就是3X3的，多维的以此类推。

![图片来自B站up主小萌Annie的视频](https://tva1.sinaimg.cn/large/008i3skNgy1gtebyvwmd7j61ii0u0q9i02.jpg)

向分类器A输入9张图片，其中：

有1张图片真实类别是汉堡，分类器预测的类别也是汉堡，所以在预测是X真实是的格子中填入样本数量1

有2张图片真实类别不是汉堡，分类器预测的类别是汉堡，所以在预测是X真实不是的格子中填入样本数量2

有1张图片真实类别是汉堡，分类器预测的类别不是汉堡，所以在预测不是X真实是的格子中填入样本数量1

有5张图片真实类别不是汉堡，分类器预测的类别不是汉堡，所以在预测不是X真实不是的格子中填入样本数量5

这样就得到了分类器A的混淆矩阵，就能简单、明了的看到分类结果了。

为了方便，我们将预测为是，或者预测类别为1，计作Positiive；预测为不是，或者预测类别为0，计作Negative。

同时预测和真实相同是，计作True，不同计作False。

这样每种预测分类的情况就有了对应的简称：TP、FP、FN、TN。

现在通过混淆矩阵，能直观的看出分类器的分类结果，那有什么指标能量化的评估分类器分类效果的好坏呢？

1. 准确率accuracy

   预测结果和真实结果相同的样本数，占总样本数的比例。

   $准确率=\frac{预测和真实相同的样本数}{总的样本数}=\frac{TP+TN}{TP+FP+FN+TN}$

2. 精确率precision，也叫查准率

   预测为是的样本中，真实也为是的占比。精确率越低，FP就越大，代表对0或者多数类误判越多。

   $精准率=\frac{预测为是、真实也为是的样本数}{预测为是的样本数}=\frac{TP}{TP+FP}$

3. 召回率recall，也叫敏感度、真正率、查全率

   真实为是的样本中，预测也为是的占比。召回率越高，代表捕捉到了越多的1类或者少数类。

   $召回率=\frac{真实为是、预测也为是的样本数}{真实为是的样本数}=\frac{TP}{TP+FN}$

可见精确率和召回率是一个此消彼长的关系，对少数类分类正确越多，就越可能对多数类误伤；对多数率分类正确越多，就越可能漏掉了很多少数类。

因此，还有一个指标F1值，是兼顾精确率和召回率，作为两者的平衡：

4. F1值

   F1值是精确率和召回率的调和平均数，F1值在0-1之间分布，越接近1越好：

   $F1值=\frac{2*精确率*召回率}{精确率+召回率}$

5. 假负率FNR（False Negative Rate）

   从召回率延伸出来的一个指标。真实为是的样本中，预测为不是的占比。假负率=1-召回率。假负率通常用的不多

   $假负率=\frac{真实为是、预测为不是的样本数}{真实为是的样本数}=\frac{FN}{TP+FN}$

6. ROC曲线

   分类器在进行分类时，是通过概率进行分类的，大于某个概率分类器认为它属于某一类，小于某个概率属于另一类。

   这个概率是人为设定的，叫阈值，取0-1之间任何一个数。

   那么人为设定不同的阈值，分类器必然会得出不同的分类结果，也就得出了多个不同的混淆矩阵。

   那有没有一种方法，可以将多个混淆矩阵表示在一个二维空间中呢？这就是用ROC曲线来表示。

   ![图片来自B站up主小萌Annie的视频](https://tva1.sinaimg.cn/large/008i3skNgy1gtedrzi4gqj61j20u0go302.jpg)

   对于一个混淆矩阵，我们可以求出他的召回率（TPR）和假正率（FPR），FPR作为横轴，TPR作为纵轴，就能在坐标系中划出一个点。

   多个混淆矩阵，就能求出多个点，这些点连起来就是ROC曲线。

   假设现在有两个分类器A和B，得到了他们各自的ROC曲线：

   ![图片来自B站up主小萌Annie的视频](https://tva1.sinaimg.cn/large/008i3skNgy1gtedyr46stj61t40tswh602.jpg)

   通过ROC曲线，如何评估哪个分类器的效果更好呢？

   TPR是所有真实为正的样本中，被分对的概率。FPR是所有真实为负的样本中，被分错的概率。

   显然我们更希望TPR越大越好，因此ROC曲线越靠近左上角越好。

   图中分类器B的效果比A好。

   曲线越靠近左上角，它与X轴所围成的面积就越大，这个面积叫做AUC。这个面积是可以被计算出来的，在0-1之间。

   所以可以通过ROC曲线的面积AUC来定量的衡量分类器的效果。

看样本均不均衡，第一个就是看召回率，第二个看ROC曲线。

### sklearn混淆矩阵以及各指标实现

使用class_weight中生成的不均匀样本数据集，以及未设置class_weight='balanced'的分类器clf1和设置了balanced的分类器2。

针对这两个分类器生成个字的混淆矩阵以及指标。

```python
# 混淆矩阵的方法都在sklearn.metrics这个类中
# sklearn.metrics.confusion_matrix() # 混淆矩阵
# sklearn.metrics.accuracy_score() # 准确率
# sklearn.metrics.precision_score() # 精准率
# sklearn.metrics.recall_score() # 召回率
# sklearn.metrics.f1_score() # F1值

# 导入混淆矩阵类
from sklearn import metrics

# 混淆矩阵
metrics.confusion_matrix(y_test,clf1.predict(x_test))
array([[183,   8],
       [ 15,  14]])
metrics.confusion_matrix(y_test,clf2.predict(x_test))
array([[183,   8],
       [ 12,  17]])

# 准确率
metrics.accuracy_score(y_test,clf1.predict(x_test))
0.8954545454545455
metrics.accuracy_score(y_test,clf2.predict(x_test))
0.9090909090909091

# 精准率
metrics.precision_score(y_test,clf1.predict(x_test))
0.6363636363636364
metrics.precision_score(y_test,clf2.predict(x_test))
0.68

# 召回率
metrics.recall_score(y_test,clf1.predict(x_test))
0.4827586206896552
metrics.recall_score(y_test,clf2.predict(x_test))
0.5862068965517241

# F1值
metrics.f1_score(y_test,clf1.predict(x_test))
0.5490196078431373
metrics.f1_score(y_test,clf2.predict(x_test))
0.6296296296296295
```

# 线性回归算法（linearRegression）

数据集标签为连续变量的学习任务，叫做回归任务（Regression）。

线性回归是解决回归类问题最常使用的算法模型，其算法思想和基本原理都是由多元统计分析发展而来。

**虽然回归问题和分类问题同属于有监督学习范畴**，但实际上，回归问题要远比分类问题更加复杂。

首先是关于输出结果的对比，分类模型最终输出结果为离散变量，而离散变量本身包含信息量较少，其本身并不具备代数运算性质，因此其评价指标体系也较为简单，最常用的就是混淆矩阵以及ROC曲线。 

而回归问题最终输出的是连续变量，其本身不仅能够代数运算，且还具有更"精致"的方法，希望对事物运行的更底层原理进行挖掘。

即回归问题的模型更加全面、完整的描绘了事物客观规律，从而能够得到 更加细粒度的结论。 

因此，回归问题的模型往往更加复杂，建模所需要数据所提供的信息量也越多，进而在建模过程中可能遇到的问题也越多。

假设一个数据集有n各样本，i个特征列X，1个标签列y

**那么他的线性回归的方程表达式：**
$$
\widehat{y} = w_0+w_1x_{i1}+w_2x_{i2}+...+w_nx_{in}
$$
w被统称为模型参数，其中：

$w_o$——被称为截距（intercept），回归线在纵坐标轴上的截距

$w_1-w_n$——被称为回归系数（regression coefficient）

$x_{i1}-x_{in}$——特征

$\widehat{y}$——标签

数据集中有多个特征维度，他的线性回归称为 **多元线性回归（或多重线性回归）**。

如果数据集中只有1个特征维度，他的线性回归就称为 **一元线性回归（或简单线性回归）**，即$y=ax+b$

机器学习中，数据一般不可能只有1个特征维度，因此一般都是使用多元线性回归。

多元线性回归的方程表达式可以简写为：$\widehat{y}=Xw$，这个预测函数就是我们要构建的模型。

其中，y和X都是已知，只有w是未知的，所以线性回归原理的核心就是找出模型的参数向量w。

## 损失函数

在之前学习的KNN和决策树中，都是根据训练集训练出模型，然后在测试集上使用模型，看模型在测试集上预测的效果来评价模型的好货。

然而，在线性回归中，通过训练集得出的模型针对训练集本身就是有误差的，因为线性回归得出的回归线只能尽量靠近所有样本点，而无法让所有样本点都在回归线上。

例如这个例子，假设我有一份训练集数据有3个样本，只有1个特征是房屋的面积，1个标签是房屋的总价格。

我们想训练出线性回归模型，来预测房屋价格。那么我们在横轴是房屋面积，纵轴是房屋价格的坐标系中可以画出3个样本点，并且我们对数据进行线性回归，得到一条回归线：

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtf89xouojj60wc0tw0u802.jpg)

我们通过数据训练出的模型，就是这条回归线，再将数据的X带入到模型就是这条线中，可以看到，模型得出的$\widehat{y}$，也就是通过线性回归预测的$\widehat{y}$，和数据本身真实的$y$是有误差的。

这个误差就是系数为w的模型拟合训练集时产生的信息损失的大小。

那么不同的系数w，就能画出不同的回归线，就得到不同的信息损失。

信息损失越小，模型就越优秀；信息损失越大，模型就越糟糕。

信息损失的大小是可以计算出来的，就是通过==损失函数==计算。

因此，线性回归模型的好坏，就通过计算损失函数来衡量了。

## 回归模型评估指标

损失函数通过最基础的SSE指标计算得出，在SSE基础上又延伸出其他指标

### SSE误差平方和

损失函数，就是求通过线性回归预测的$\widehat{y}$，和数据本身真实的$y$的==误差平方和==（SSE，sum of the standard error）:
$$
SSE=(y_1-\widehat{y}_1)^2+(y_2-\widehat{y}_2)^2+...+(y_n-\widehat{y}_n)^2
$$
例如上图中，这个模型的SSE就是$(200-120)^2+(390-300)^2+(500-480)^2=14900$

所以SSE越小，预测值和真实值的误差就越小，模型效果就越好。

### MSE均方误差

如果样本个数增大，显然SSE就越大，用SSE除以样本个数，就能抵消样本个数增大导致SSE变大的影响了，这就叫==均方误差==（MSE，mean standard error）
$$
MSE=\frac{1}{n}[(y_1-\widehat{y}_1)^2+(y_2-\widehat{y}_2)^2+...+(y_n-\widehat{y}_n)^2]
$$
图中例子，这个模型的MSE就是$\frac{1}{3}[(200-120)^2+(390-300)^2+(500-480)^2]=4966$

### RMSE均方根误差

对MSE进一步处理，MSE使用了平方来避免误差有正有负相抵的情况，因此对MSE进行开平方，就得到了==均方根误差==（RMSE，root mean standard error）
$$
RMSE=\sqrt[2]{\frac{1}{n}[(y_1-\widehat{y}_1)^2+(y_2-\widehat{y}_2)^2+...+(y_n-\widehat{y}_n)^2]}
$$

### MAE平均绝对误差

SSE中对误差进行平方再求和，就是为了避免正误差和负误差相抵消的情况。那么我们以可以对误差求绝对值再求和再平均，就得到了==平均绝对误差==（mean absoulte error）
$$
MAE=\frac{1}{n}[|y_1-\widehat{y}_1|+|y_2-\widehat{y}_2|+...+|y_n-\widehat{y}_n|]
$$

### $R^2$决定系数

![](https://tva1.sinaimg.cn/large/008i3skNgy1gtfap0yvjpj61om0u00vv02.jpg)

还是房屋面积和总价的例子，首先根据3个样本的真实y值，我们可以得出所有样本的平均y值$\bar{y}$。

每个样本真实的y值➖平均值$\bar{y}$，对其平方，再将所有结果相加，得到的就是TSS。

RSS是，每个样本的预测值和真实值的差的平方和。

最后，$R^2=\frac{TSS-RSS}{TSS}$

我们希望RSS越小越好，所以$R^2$越大说明模型效果越好。

## 最小二乘法

找到最优的回归模型，就是找到让SSE最小的参数向量w，这种通过最小化SSE来求解参数的方法叫做最小二乘法。

## sklearn实现一元线性回归

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"

# 代码实现一元线性回归

# 导入包
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

```python
# 生成一元训练集数据

# 生成1维数组，50个0-1之间的随机数，并且放大10倍，作为特征列X的数据
X = np.random.rand(50) * 10

# 假设一个一元线性方程，根据X取出y，作为训练集的真实标签
# +np.random.randn(50)是因为，方程本身就是一元线性的，绘制出(x,y)的点本身就是一条直线
# 添加扰动项，让y偏移，使图形不在一条直线上。让数据有一定随机性
y = 2 * X - 5 + np.random.randn(50)

# 将训练集数据画在图上看看
plt.plot(X,y,'o')
```

![img](data:image/png;base64,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)

```python
# 开始训练线性回归模型
from sklearn.linear_model import LinearRegression

lr = LinearRegression(fit_intercept=True)
lr.fit(X.reshape(-1,1),y)

# 查看训练得出的模型的属性

# coef_ 斜率
lr.coef_
array([[1.97956849]])

# intercept_ 截距
lr.intercept_
-4.947993909655161
```

```python
# 绘制模型的回归线
# 生成x横坐标，0-10之间生成100个数
x = np.linspace(0,10,100)
# 用模型生成对应的y值
y_pred = lr.predict(x.reshape(-1,1))

plt.plot(x,y_pred)
plt.plot(X,y,'o')
```

![img](data:image/png;base64,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)

## sklearn实现加州房价线性回归

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = 'all'

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split,cross_val_score
from sklearn.datasets import fetch_california_housing

# 下载数据
house = fetch_california_housing()
house

# 数据整理成DataFrame
X = pd.DataFrame(house.data,columns=house.feature_names)
y = house.target

# 划分训练集和测试集
X_train,X_test,y_train,y_test = train_test_split(X,y,test_size=0.3,random_state=420)
X_train.head()
```

|       | MedInc | HouseAge | AveRooms | AveBedrms | Population | AveOccup | Latitude | Longitude |
| ----: | -----: | -------: | -------: | --------: | ---------: | -------: | -------: | --------: |
| 17073 | 4.1776 |     35.0 | 4.425172 |  1.030683 |     5380.0 | 3.368817 |    37.48 |   -122.19 |
| 16956 | 5.3261 |     38.0 | 6.267516 |  1.089172 |      429.0 | 2.732484 |    37.53 |   -122.30 |
| 20012 | 1.9439 |     26.0 | 5.768977 |  1.141914 |      891.0 | 2.940594 |    36.02 |   -119.08 |
| 13072 | 2.5000 |     22.0 | 4.916000 |  1.012000 |      733.0 | 2.932000 |    38.57 |   -121.31 |
|  8457 | 3.8250 |     34.0 | 5.036765 |  1.098039 |     1134.0 | 2.779412 |    33.91 |   -118.35 |

```python
# 训练模型
lr = LinearRegression()
lr.fit(X_train,y_train)
y_train_pred = lr.predict(X_train) # 训练集的预测值
y_test_pred = lr.predict(X_test) # 测试集的预测值
```

```python
# 查看评估指标,评估模型拟合情况

# MSE均方误差
from sklearn.metrics import mean_squared_error 

mean_squared_error(y_train,y_train_pred) # 训练集的MSE，真实值，预测值
0.5218522662533102
mean_squared_error(y_test,y_test_pred) # 测试集的MSE
0.5309012639324571
# 10折交叉验证的平均MSE
# scoring参数，表示要计算的指标，我们要计算的是mse，但是该参数的值中并没有mse，但是有neg_mse
# 就是负的mse，因此我们使用neg_mse
mse_10cv = cross_val_score(lr,X_train,y_train,scoring='neg_mean_squared_error',cv=10)
mse_10cv
array([-0.52730876, -0.50816696, -0.48736401, -0.49269076, -0.56611205,
       -0.53795641, -0.48253409, -0.5130032 , -0.53188562, -0.60443733])
mse_10cv.mean()
-0.525145918217335


# MAS平均绝对误差
from sklearn.metrics import mean_absolute_error

mean_absolute_error(y_train,y_train_pred) # 训练集mae
0.5309427617356032
mean_absolute_error(y_test,y_test_pred) # 测试集mae
0.5307069814636165
# 10折交叉验证的平均MAS，同样scoring只有neg_mae
mae_10cv = cross_val_score(lr,X_train,y_train,scoring='neg_mean_absolute_error',cv=10)
mae_10cv.mean()
-0.5313931576388838


# R2决定系数
from sklearn.metrics import r2_score

r2_score(y_train,y_train_pred) # 训练集r2
0.6067440341875014
r2_score(y_test,y_test_pred) # 测试集r2
0.6043668160178817
# 模型的score返回的就是r2
lr.score(X_train,y_train) # 训练集score
0.6067440341875014
lr.score(X_test,y_test) # 测试集score
0.6043668160178817
# 10这交叉验证的平均r2
r2_10cv = cross_val_score(lr_cross,X_train,y_train,scoring='r2',cv=10)
r2_10cv.mean()
0.6039238235546339
```

```python
# 查看模型系数
# 斜率
lr.coef_
list(zip(X.columns,lr.coef_))
[('MedInc', 0.43735893059684033),
 ('HouseAge', 0.010211268294493994),
 ('AveRooms', -0.10780721617317697),
 ('AveBedrms', 0.6264338275363783),
 ('Population', 5.216125353261353e-07),
 ('AveOccup', -0.003348509646333585),
 ('Latitude', -0.41309593789477195),
 ('Longitude', -0.42621095362084704)]
# 这就是每个特征对应的回归系数，比如第一个特征MedInc，当这个特征提升1个点，对y就有0.437的影响

# 截距
lr.intercept_
-36.256893229203875
```

**对数据集标准化后再训练**

```python
# 数据标准化
from sklearn.preprocessing import StandardScaler
ss = StandardScaler()

X_train_std = ss.fit_transform(X_train) # 训练集数据标准化
X_test_std = ss.fit_transform(X_test) # 测试集数据标准化

# 使用标准化后的数据训练模型
lr.fit(X_train_std,y_train)
lr.score(X_train_std,y_train) # 查看评分，就是r2
0.6067440341875014
```

**绘制拟合图像**

```python
# 绘制拟合图像
# 将数据真实的标签列数据y_test，和模型预测的标签列数据y_test_pred绘制到一个图中比对
# 绘制方法：
# x轴是0到y_test长度的序列，y轴是将y_test排序后的序列
# np.argsort(y_test)返回y_test排序后的索引序列，y_test_pred[np.argsort(y_test)]就能将y_test_pred和sorted(y_test)对应起来

# 蓝色是真实y值
plt.scatter(range(len(y_test)),sorted(y_test),s=2)

# 橙色是预测y值
plt.scatter(range(len(y_test)),y_test_pred[np.argsort(y_test)],s=2,alpha=0.3)
```

![img](data:image/png;base64,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)

## 多重共线性问题

多重共线性：特征与特征之间高度相关。

当回归模型中两个或两个以上的自变量彼此相关时，则称回归模型中存在**多重共线性**，也就是说共线性的自变量提供了重复的信息。

那么这种多重共线性会有什么不好的影响吗？答案是会的，而且影响非常不好。总结一下就是：会造成回归系数，截距系数的估计非常不稳定，即整个模型是不稳定。

这种不稳定的具体表现是：很可能回归系数原来正，但因为共线性而变为负。这对于一些自变量的可解释性来讲可能是致命的，因为得到错误系数无法解释正常发生的现象。
————————————————
版权声明：本文为CSDN博主「Python数据科学」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/yuxiaosmd/article/details/112306519