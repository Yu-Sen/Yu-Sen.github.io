---
title: 【数据分析】第四阶段：大数据查询利器Hive（二）
date: 2021-05-27 23:58:33
tags:
- 数据分析
categories:
- 学习笔记
description: apache hive
---

# Hive简介

Hive是facebook为了解决海量日志数据的统计分析开发的基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供类SQL查询功能。

hive没有存储数据的能力，只有使用数据的能力。

# Hive运行机制

![](Xnip2021-05-28_00-20-08)

![](Xnip2021-05-28_00-21-46)

Hive 通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的 Driver， 结合元数据(MetaStore)，将这些指令翻译成 MapReduce，提交到 Hadoop 中执行，最后，将 执行返回的结果输出到用户交互接口。

# HQL

## HQL和SQL区别

注释只能用`--`。

每个语句必须`;`结尾。

## 创建数据库

```
#CREATE DATABASE [IF NOT EXISTS] 数据库名; 
create database lagou;
```

## 查看数据库

```
SHOW DATABASES;
```

## 删除数据库

### 删除空数据库（没有数据）

```
--drop database 数据库名; 
drop database lagou; 

--drop database if exists 数据库名; 
drop database if exists lagou;

--查看所有数据库
show databases;
```

### 强制删除数据库（有数据）

```
--drop database 数据库名 cascade; 
drop database lagou cascade;
```

## 使用数据库

```
--use 数据库名 
use lagou;
```

## HQL数据类型

![](Xnip2021-05-28_00-34-53)

![](Xnip2021-05-28_00-35-07)

![](Xnip2021-05-28_00-35-45)

![](Xnip2021-05-28_00-36-07)

![](Xnip2021-05-28_00-36-23)