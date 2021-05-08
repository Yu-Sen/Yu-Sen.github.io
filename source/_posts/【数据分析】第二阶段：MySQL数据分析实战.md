---
title: 【数据分析】第二阶段：MySQL数据分析实战
date: 2021-05-04 17:40:02
tags:
- 学习笔记
categories:
- 数据分析
description: 基础语句、聚合、分组、约束、多表查询
---

# MySQL基础

## 数据库基本概念

数据库是按照数据结构来组织、存储和管理数据的仓库。其本质是一个文件系统，以文件的形式将数据保存在电脑上。

```mermaid
graph LR
文件 --> 文件系统 --> 计算机磁盘
```

数据库（DB），数据库管理系统（DBMS）

关系型数据库（RDB）：关系型数据库是创建在关系模型基础上的数据库。

关系模型：有明确的行和列的二维表格模型。（就像excel表一样）

IOE：IBM小型机，Oracle数据库，EMC易安信。

客户端与服务端建立连接；写SQL，执行SQL语句操作MySQL数据库。

字段：表中每一列成为一个字段，字段有自己的属性，如字段类型、字段大小等，字段类型决定了字段能够存储哪种数据。字段属性在建表时就必须定义好。

索引：索引是一种单独的、物理的数据库结构。它依赖于表建立。

视图：视图是从一张或多张表中到处的表（也称虚拟表），是用户产看表中数据的一种方式。

## MySQL数据库

MySQL是C和C++编写的。

MySQL支持多线程。

MySQL存储引擎：存储引擎是MySQL数据库的核心。它决定了数据如何存储，查询的时候如何搜索数据，索引如何创建等等。

常用存储引擎：

- InnoDB：MySQL5.1版本之后默认存储引擎。
- MyISAM：MySQL5.1版本之前默认存储引擎。
- Memory

MySQL数据类型主要分为数值型、字符串型、日期时间型三大类。

字符串型char和varchar：

- char固定长度，char(5)会在内存中占用5个字符的空间，即使只存入了3个字符。

- varchar可变长度，varchar(5)存入3个字符，就只占用3个字符的空间。
- char浪费空间，但查询时整体匹配，效率更高，比如存入abc，查询时会直接匹配整体是否是abc。
- varchar节省空间，但查询时不是整体匹配，效率不高，比如存入abc，查询时先匹配a再匹配b再匹配c。

MySQL Server安装目录下，bin目录下的mysql就是自带的命令行客户端。

访问数据库命令行：mysql -h127.0.01 -uroot -p123456

- mysql：使用mysql客户端访问服务端
- -h127.0.01：当前服务端是本机，所以使用-h127.0.0.1指定访问本地127.0.0.1地址的服务。因为服务端在本地，这部分也可以不写会自动访问本地服务。
- -uroot：登陆用户是root
- -p123456：登陆密码是123456

# SQL语句

SQL语句按功能分为以下几类：

- DDL语句（Data Definition Language 数据定义语言)
- DML语句（Data Manipulation Language 数据操作语言)
- DQL语句（Data QueryLanguage 数据查询语言)
- DCL语句（Data Controll Language 数据控制语言)

**SQL语句的执行顺序：1、最先执行from tab；2、where语句是对条件加以限定；3、分组语句【group by…… having】；4、聚合函数；5、select语句；6、order by排序语句。**

## 增删改

```sql
-- 新建数据库 create database 数据库名;
CREATE DATABASE db1;
-- 新建数据库，并指定字符集 create database 数据库名 character set 字符集;
CREATE DATABASE db1_1 CHARACTER SET utf8;

-- 切换数据库 从db1 切换到 db1_1 
USE db1_1;
-- 查看当前正在使用的数据库 
SELECT DATABASE();
-- 查看Mysql中有哪些数据库 
SHOW DATABASES;
-- 查看一个数据库的定义信息 
SHOW CREATE DATABASE db1_1;

-- 将数据库db1 的字符集 修改为 utf8 
ALTER DATABASE db1 CHARACTER SET utf8;
-- 查看当前数据库的基本信息，发现编码已更改 
SHOW CREATE DATABASE db1;

-- 删除某个数据库 
DROP DATABASE db1_1;

-- 创建表
/* CREATE TABLE 表名(
字段名称1 字段类型(长度)，
字段名称2 字段类型 注意 最后一列不要加逗号
);*/
CREATE TABLE category(
cid INT,
    cname VARCHAR(20)
);

/* 
快速创建一个表结构相同的表(仅复制表结构，不会复制数据)
create table 新表名 like 旧表名;
*/
-- 创建一个表结构与 test1 相同的 test2表 
CREATE TABLE test2 LIKE test1;
-- 查看表结构 
DESC test2;

-- 查看当前数据库中的所有表名 
SHOW TABLES;
-- 显示当前数据表的结构 
DESC category;
-- 查看创建表的SQL语句
SHOW CREATE TABLE category;

-- 直接删除 test1 表 
DROP TABLE test1;
-- 先判断表是否存在 再删除test2表
DROP TABLE IF EXISTS test2;

-- 修改表名 rename table 旧表名 to 新表名;
RENAME TABLE category TO category1;

-- 向表中添加列 alter table 表名 add 字段名称 字段类型
# 为分类表添加一个新的字段为 分类描述 cdesc varchar(20) 
ALTER TABLE category ADD cdesc VARCHAR(20);

-- 修改表中列的数据类型或长度 alter table 表名 modify 字段名称 字段类型
ALTER TABLE category MODIFY cdesc VARCHAR(50);

-- 修改列名称 alter table 表名 change 旧列名 新列名 类型(长度);
ALTER TABLE category CHANGE cdesc description VARCHAR(30);

-- 删除列 alter table 表名 drop 列名;
ALTER TABLE category DROP description;

-- 插入数据
-- insert into 表名 (字段名1，字段名2...) values(字段值1，字段值2...);
-- 方式1: 插入全部字段， 将所有字段名都写出来
INSERT INTO student (sid,sname,age,sex,address) VALUES(1,'孙悟 空 ',20,'男 ','花 果 山 ');
-- 方式2: 插入全部字段，不写字段名
INSERT INTO student VALUES(2,'孙悟饭',10,'男','地球');
-- 方式3:插入指定字段的值
INSERT INTO category (cname) VALUES('白骨精');

-- 更改数据
-- 不带条件的修改 update 表名 set 列名 = 值
UPDATE student SET sex = '女';
-- 带条件的修改 update 表名 set 列名 = 值 [where 条件表达式:字段名 = 值 ]
UPDATE student SET sex = '男' WHERE sid = 3;
-- 一次修改多个列
UPDATE student SET age = 20,address = '北京' WHERE sid = 2;

-- 删除数据
delete from 表名;
truncate table 表名;
-- 指定条件 删除数据
delete from 表名 [where 字段名 = 值];
```

**当一个表中数据条数非常多，又要做删除动作的时候，如果用delete会很慢，那我们可以使用 truncate 。**

**delete from 表名; 不推荐. 有多少条记录 就执行多少次删除操作. 效率低。**

**truncate table 表名: 推荐. 先删除整张表, 然后再重新创建一张一模一样的表. 效率高。**

## 查

```sql
-- 查询所有数据
select * from 表名
-- 查询某一列
select 列名1,列名2 from 表名
-- 使用 AS关键字,为列起别名 
SELECT eid AS '编号', ename AS '姓名' , sex AS '性别' FROM emp;
-- AS 可以省略 
SELECT eid '编号', ename '姓名' , sex '性别' FROM emp;
-- 使用distinct 关键字,去掉重复部门信息 
SELECT DISTINCT dept_name FROM emp;
-- 运算查询 (查询结果参与运算)
SELECT ename , salary + 1000 FROM emp;
```

| **比较运算符**     | **说明**                                                     |
| ------------------ | ------------------------------------------------------------ |
| \> < <= >= = <> != | 大于、小于、小于(大于)等于、等于、不等于                     |
| BETWEEN ...AND...  | 显示在某一区间的值 <br />例如: 2000-10000之间: Between 2000 and 10000 |
| IN(集合)           | 集合表示多个值,使用逗号分隔,<br />例如: name in (悟空，八戒) in中的每个数据都会作为一次条件,只要满足条件就会显示 |
| LIKE '%张%'        | 模糊查询<br />通配符%，表示匹配任意多个字符串<br />通配符_，表示匹配一个字符<br />where name like '%王%'; -- name中有王的<br />where name like '王%'; -- name中以王开头的<br />where name like '\_王%'；-- name中第二个字是王的 |
| IS NULL            | 查询某一列为NULL的值, 注: 不能写 = NULL                      |
| **逻辑运算符**     |                                                              |
| And &&             | 多个条件同时成立                                             |
| Or \|\|            | 多个条件任一成立                                             |
| Not                | 不成立，取反。<br />和IS NULL组合使用，就是查询字段不是Null的数据。<br />where dept_name IS NOT NULL; |

## 核心查询

### 排序

```
SELECT 字段名 FROM 表名 [WHERE 字段 = 值] ORDER BY 字段名 [ASC / DESC]
```

```sql
-- 默认升序排序 ASC
SELECT * FROM emp ORDER BY salary;
-- 降序排序
SELECT * FROM emp ORDER BY salary DESC;
-- 组合排序
SELECT * FROM emp ORDER BY salary DESC, eid DESC;
```

Order by不会影响源数据，只用于展示结果。

### 聚合

也称为聚合统计或者聚合查询，使用聚合函数纵向查询，对某一列的值进行计算，然后返回一个单一的值(另外聚合函数会忽略null空 值。

聚合函数：

- count(字段) 统计指定列不为NULL的记录行数。
- sum(字段) 计算指定列的数值和。
- max(字段) 计算指定列的最大值。
- min(字段) 计算指定列的最小值。
- avg(字段) 计算指定列的平均值

```
SELECT 聚合函数(字段名) FROM 表名;
```

```sql
#1 查询员工的总数
-- 统计表中的记录条数 使用 count()
SELECT COUNT(eid) FROM emp; -- 使用某一个字段 
SELECT COUNT(*) FROM emp; -- 使用 *
SELECT COUNT(1) FROM emp; -- 使用 1,与 * 效果一样
-- 下面这条SQL 得到的总条数不准确,因为count函数忽略了空值 
-- 所以使用时注意不要使用带有null的列进行统计
SELECT COUNT(dept_name) FROM emp;

#2 查看员工总薪水、最高薪水、最小薪水、薪水的平均值
-- sum函数求和, max函数求最大, min函数求最小, avg函数求平均值 
SELECT
	SUM(salary) AS '总薪水', 
	MAX(salary) AS '最高薪水', 
	MIN(salary) AS '最低薪水', 
	AVG(salary) AS '平均薪水'
FROM emp;

#3 查询薪水大于4000员工的个数
SELECT COUNT(*) FROM emp WHERE salary > 4000;

#4 查询部门为'教学部'的所有员工的个数
SELECT COUNT(*) FROM emp WHERE dept_name = '教学部';

#5 查询部门为'市场部'所有员工的平均薪水 SELECT
AVG(salary) AS '市场部平均薪资' FROM emp WHERE dept_name = '市场部';
```

**⚠️注意：where中不能使用聚合函数，只能通过子查询实现**。[https://blog.csdn.net/fanbaodan/article/details/84304782](https://blog.csdn.net/fanbaodan/article/details/84304782)

### 分组

分组查询指的是使用GROUP BY 语句，对查询的信息进行分组，相同数据作为一组。

```sql
-- 语法
SELECT 分组字段/聚合函数 FROM 表名 [where 条件] GROUP BY 分组字段 [HAVING 条件];
-- select后必须出现要分组的字段，除了分组字段、聚合函数，不能出现其他字段
-- where先对源数据过滤，having对分组聚合统计后的数据再过滤
```

分组的目的是为了统计，所以分组一般会与聚合函数一起使用。只进行分组是没有意义的。

```sql
-- 只分组不统计 这样写没有意义
SELECT * FROM emp GROUP BY sex; 
-- 分组返回每组的第一条数据
```

示例：

```sql
#1 通过性别字段 进行分组,求各组的平均薪资
select sex,avg(salary)'平均薪资' from emp GROUP BY sex;

#2 查询每个部门的平均薪资 
select dept_name'部门',avg(salary)'平均薪资' from emp GROUP BY dept_name;

#3 查询每个部门的平均薪资, 部门名称不能为null
select dept_name'部门',avg(salary)'平均薪资' from emp where dept_name is not null GROUP BY dept_name;

#4 查询平均薪资大于6000的部门
-- 先对源数据where过滤掉部门为null的数据，接着按部门分组聚合统计各部门的平均薪资，最后再通过having过滤出平均薪资大于6000的部门。
SELECT
	dept_name '部门',
	avg( salary ) '平均薪资' 
FROM
	emp 
WHERE
	dept_name IS NOT NULL 
GROUP BY
	dept_name 
HAVING
	avg( salary ) > 6000;
```

**where和having的区别**

- where 进行分组前的过滤，where 后面不能写聚合函数
- having 是分组后的过滤，having 后面可以写聚合函数

### limit

limit是限制的意思,用于限制返回的查询结果的行数 (可以通过limit指定查询多少行数据)

limit 语法是 MySql的方言,用来完成分页

```sql
SELECT 字段1,字段2... FROM 表名 LIMIT offset , length;
```

offset 起始行数，从0开始记数， 如果省略 则默认为 0。
length 返回的行数。

```sql
# 查询emp表中的前5条数据
-- 参数1:起始值,默认是0; 参数2:要查询的条数 
SELECT * FROM emp LIMIT 5;
SELECT * FROM emp LIMIT 0 , 5;
# 查询emp表中 从第4条开始,查询6条 -- 起始值默认是从0开始的.
SELECT * FROM emp LIMIT 3 , 6;
```

**分页**

```sql
-- 分页操作 每页显示3条数据
SELECT * FROM emp LIMIT 0,3; -- 第1页 
SELECT * FROM emp LIMIT 3,3; -- 第2页
SELECT * FROM emp LIMIT 6,3; -- 第3页
-- 分页公式 起始索引 =(当前页 -1)* 每页条数 
-- limit是MySql中的方言
/*
分页，每页显示3条
第1页，1，2，3
第2页，4，5，6
第3页，7，8，9
第1页，第1条开始，相对第1条偏移0条，显示3条
第2页，第4条开始，相对第1条偏移3条，显示3条
第3页，第7条开始，相对第1条偏移6条，显示3条
当前页偏移量=当前页之前所有页的显示条数总和
当前页偏移量计算公式：（页数-1）*每页显示条数
*/
```

## SQL约束

约束，就是对数据进一步限制，从而保证数据的有效性、正确性、完整性。违反约束的数据将无法插入到数据表中。

约束是针对字段的。

常见约束：

- 主键（primary key）
- 唯一（unique）
- 非空（not null）
- 外键（foreign key）
- 默认值（default）

### 主键

作用：用来唯一标识数据表中的每一条记录。

特点：不可重复，唯一，非空。

```
字段名 字段类型 primary key
primary key(字段名)
```

```sql
# 方式1 创建一个带主键的表 
CREATE TABLE emp2(eid INT PRIMARY KEY, ename VARCHAR(20));

# 方式2 创建一个带主键的表 
CREATE TABLE emp2(eid INT, ename VARCHAR(20), PRIMARY KEY(eid));

# 方式3 向已经创建的表添加主键
ALTER TABLE emp2 ADD PRIMARY KEY(eid);

-- 删除表中的主键
ALTER TABLE emp2 DROP PRIMARY KEY;
```

#### 主键自增

```
关键字:
AUTO_INCREMENT 表示自动增长(字段类型必须是整数类型)
```

数据库在插入数据时，如果主键自增字段不填写数据，数据库会以自增的形式自动生成主键字段的值。默认从1或者上一条记录的主键字段值开始自增，也可以指定起始值。

```sql
-- 创建主键自增的表 
CREATE TABLE emp2(
  -- 关键字 AUTO_INCREMENT,主键类型必须是整数类型 
  eid INT PRIMARY KEY AUTO_INCREMENT,
  ename VARCHAR(20),
  sex CHAR(1)
);

-- 创建主键自增的表,自定义自增起始值 
CREATE TABLE emp2(
  eid INT PRIMARY KEY AUTO_INCREMENT,
  ename VARCHAR(20),
  sex CHAR(1)
)AUTO_INCREMENT=100;
```

**delete**删除数据不会影响自增，自增会接着删除前的数据自增。

**truncate**删除数据再自增会从1开始，因为truncate是将表删除再复制一个表结构相同的新表。

### 非空

特点：该列不允许为null

```
字段名 字段类型 not null
```

```sql
# 非空约束
CREATE TABLE emp2(
  eid INT PRIMARY KEY AUTO_INCREMENT,
  -- 添加非空约束, ename字段不能为空 
  ename VARCHAR(20) NOT NULL,
  sex CHAR(1)
);
```

**空字符串('')不等于null，可以插入**。

```sql
# 插入''成功
insert into emp2 (ename,sex) values('','男');	-- OK, Time: 0.000000s
```

### 唯一

特点：该列的值不能重复（对null会忽略不做判断）

```
字段名 字段值 unique
```

```sql
#创建emp3表 为ename 字段添加唯一约束 
CREATE TABLE emp3(
  eid INT PRIMARY KEY AUTO_INCREMENT,
  ename VARCHAR(20) UNIQUE,
  sex CHAR(1)
);
```

主键约束和唯一约束的区别：

- 主键约束，唯一且不能够为空(主键约束=非空约束+唯一约束)。
- 唯一约束，唯一 但是可以为空。
- 一个表中只能有一个主键，但是可以有多个唯一约束。

### 默认值

作用：用来指定该列的默认值。

```
字段名 字段类型 DEFAULT 默认值
```

```sql
-- 创建带有默认值的表 
CREATE TABLE emp4(
	eid INT PRIMARY KEY AUTO_INCREMENT, 
	ename VARCHAR(20),
  -- 为sex字段添加默认值
	sex CHAR(1) DEFAULT '女'
);

-- 添加数据 使用默认值
INSERT INTO emp4(ename,sex) VALUES('张三',DEFAULT); 
INSERT INTO emp4(ename) VALUES('李四');
```

### 外键约束

数据表A中某一个字段，指向数据表B中的主键，那么数据表A中的这一字段就是外键。

在这种关系场景中，外键所在的表叫做从表，外键所指向表叫做主表。

作用：使两张数据表产生对应关系。

特点：

- 从表插入数据时，外键字段的值必须在主表主键字段中存在，外键字段类型必须和主表主键字段类型保持一致。

- 一张表可以有多个外键。表明该表与多个表有关联。

```sql
-- 新建表时添加外键
[CONSTRAINT] [外键约束名称] FOREIGN KEY(外键字段名) REFERENCES 主表名(主键字段名);
-- 已有表添加外键
ALTER TABLE 从表名 ADD [CONSTRAINT] [外键约束名称] FOREIGN KEY (外键字段名) REFERENCES 主表(主键字段名);

-- 删除外键
alter table 从表名 drop foreign key 外键约束名称

-- 添加外键
ALTER TABLE 从表 ADD [CONSTRAINT] [外键约束名称] FOREIGN KEY (外键字段名) REFERENCES 主表(主 键字段名);
```

```sql
-- 添加外键约束，创建employee表
CREATE TABLE employee(
  eid INT PRIMARY KEY AUTO_INCREMENT,
  ename VARCHAR(20),
  age INT,
  dept_id INT, -- 外键字段类型要和主表的主键字段类型保持一致 
  -- 添加外键约束
  CONSTRAINT emp_dept_fk FOREIGN KEY(dept_id) REFERENCES department(id)
);

-- 添加数据 (从表外键对应主表主键)
INSERT INTO employee (ename, age, dept_id) VALUES ('张百万', 20, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('赵四', 21, 1); 
INSERT INTO employee (ename, age, dept_id) VALUES ('广坤', 20, 2); 
INSERT INTO employee (ename, age, dept_id) VALUES ('小斌', 20, 2); 

-- 插入一条有问题的数据 (部门id在主表主键中不存在)
-- Cannot add or update a child row: a foreign key constraint fails 
INSERT INTO employee (ename, age, dept_id) VALUES ('错误', 18, 3);

-- 删除employee 表中的外键约束,外键约束名 emp_dept_fk 
ALTER TABLE employee DROP FOREIGN KEY emp_dept_fk;

-- 可以省略外键名称, 系统会自动生成一个
ALTER TABLE employee ADD FOREIGN KEY (dept_id) REFERENCES department (id);
```

**注意事项**

- 添加数据时, 应该先添加主表中的数据。
- 删除数据时,应该先删除从表中的数据。
- 从表外键类型必须与主表主键类型一致 否则创建失败。

#### 级联删除

删除主表数据的同时，自动删除掉从表数据

```
级联删除
ON DELETE CASCADE
```

```sql
-- 重新创建添加级联操作 
CREATE TABLE employee(
  eid INT PRIMARY KEY AUTO_INCREMENT,
  ename VARCHAR(20),
  age INT,
  dept_id INT,
  CONSTRAINT emp_dept_fk FOREIGN KEY(dept_id) REFERENCES department(id) -- 添加级联删除
  ON DELETE CASCADE
);
```

## 多表查询

查询多张表(至少涉及2张表),获取到需要的数据。

### 交叉连接查询

直接使用select语句查询两张表。

```
SELECT 字段名 FROM 表1, 表2;
```

交叉连接查询会产生笛卡尔积，查询结果有误

#### 笛卡尔积

![img](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-05_19-44-43.jpg)

### 内连接查询

#### 隐式内连接

交叉连接查询+where条件过滤

```
SELECT 字段名 FROM 左表, 右表 WHERE 连接条件;
```

```sql
# 隐式内连接
SELECT * FROM products,category WHERE category_id = cid;

#查询 格力空调是属于哪一分类下的商品
SELECT 
p.pname, c.cname
FROM 
products p , category c 
WHERE 
p.category_id = c.cid 
AND p.pname = '格力空调';
```

#### 显示内连接

使用**[inner] join ... on ...关键字**。

```
SELECT 字段名 FROM 左表 [INNER] JOIN 右表 ON 条件 -- inner 可以省略
```

```sql
# 显式内连接查询
SELECT * FROM products p INNER JOIN category c ON p.category_id = c.cid;

# 查询鞋服分类下,价格大于500的商品名称和价格
SELECT 
p.pname,p.price
FROM products p INNER JOIN category c 
ON p.category_id = c.cid 
WHERE p.price > 500 AND cname = '鞋服';
```

### 外连接查询

#### 左外连接

使用left [outer] join ... on ...关键字

```
SELECT 字段名 FROM 左表 LEFT [OUTER] JOIN 右表 ON 条件
```

特点：

- 左表为基准, 右表去查询左表
- 返回结果时，左表数据正常展示，右表在左表中查询得到的数据正常展示，查询不到的展示为Null

```sql
# 左外连接查询
select * from category c left join products p on c.cid = p.category_id;

# 分类表中有汽车类目，商品表中并并没有该类目的商品
c001	家电	p001	小 米 电 视 机 	5000	1	c001
c001	家电	p002	格 力 空 调 	3000	1	c001
c001	家电	p003	美 的 冰 箱 	4500	1	c001
c002	鞋服	p004	篮 球 鞋 	800	1	c002
c002	鞋服	p005	运 动 裤 	200	1	c002
c002	鞋服	p006	T 恤 	300	1	c002
c002	鞋服	p007	冲 锋 衣 	2000	1	c002
c003	化妆品	p008	神 仙 水 	800	1	c003
c003	化妆品	p009	大 宝 	200	1	c003
c004	汽车	

# 查询每个分类下的商品个数
select 
c.cname, count(p.pid) 
from 
category c left join products p 
on c.cid = p.category_id 
group by c.cname;
```

#### 右外连接

使用 right [outer] join ... on ... 关键字

```
SELECT 字段名 FROM 左表 RIGHT [OUTER] JOIN 右表 ON 条件
```

特点：和左外连接一样，只是以右表为基准。

### 小结

![img](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-05_21-50-07.jpg)

2个以上表如何多表查询，思路是先选2个进行查询，将查询结果当作一个新的表，再和下一个表进行多表查询，以此类推。

### join on and 和 join on where and

在使用left join时，on和where条件的区别如下：  

1、on条件是在生成临时表时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。（实际上左连接中如果and语句是对左表进行过滤的，那么不管真假都不起任何作用。如果是对右表过滤的，那么左表所有记录都返回，右表筛选以后再与左表连接返回） 

2、where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉，on后的条件用来生成左右表关联的临时表，where后的条件对临时表中的记录进行过滤。

 在使用inner join时，不管是对左表还是右表进行筛选，on and和on where都会对生成的临时表进行过滤。

————————————————
版权声明：本文为CSDN博主「行者摩罗」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xingzhemoluo/article/details/39677891

## 合并查询

union和union all

union/union all操作符用来合并多个select语句的结果集（就是查询结果）。

union 和 union all 都要满足以下条件才能合并：

- 要合并的结果集，列数必须相同，列的顺序必须一致，列的字段类型也必须相似。

union 和 union all 的区别：

- union：消除重复行，重复检查期间，null值会被算在内。
- union all：不消除重复行，只是将结果集合并后就返回。
- 执行效率上，union all 要比 union 快很多。

```sql
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
UNION / UNION ALL
SELECT column1 [, column2 ]
FROM table1 [, table2 ]
[WHERE condition]
```

## 子查询

子查询概念：一条select 查询语句的结果, 作为另一条 select 语句的一部分。

特点：

- 子查询必须放在小括号中
- 整个sql至少会有两个select关键字

子查询常见分类

- **where型子查询**：将子查询的结果, 作为父查询的比较条件
- **from型子查询 **：将子查询的结果, 作为 一张表,提供给父层查询使用
- **exists型子查询**：子查询的结果是单列多行, 类似一个数组, 父层查询使用 IN 函数 ,包含子查询的结果

### where型子查询

当子查询的结果是一个值时（单行单列）

```
SELECT 查询字段 FROM 表 WHERE 字段 = (子查询);
```

```sql
# 子查询的方式, 查询价格最高的商品信息
select * from products where price = (select max(price) from products);

# 查询化妆品分类下的 商品名称 商品价格
-- 子查询
select p.pname, p.price from products p where p.category_id = (select cid from category where cname = '化妆品');
-- 内连接查询
select  p.pname,p.price from products p join category c on p.category_id = c.cid where c.cname = '化妆品';
```

### exists型子查询

当子查询的结果是一个集合时（多行单列），需要用where ... in (子查询)

```
SELECT 查询字段 FROM 表 WHERE 字段 in (子查询);
```

```sql
# 查询价格小于两千的商品,来自于哪些分类(名称)
select cname from category where cid in (select category_id from products where price < 2000);

# 查询家电类 与 鞋服类下面的全部商品信息
select * from products where category_id in (select cid from category where cname in('家电','鞋服'));
```

### from型子查询

当子查询的结果是一个表（多列多行）

```
SELECT 查询字段 FROM (子查询)表别名 WHERE 条件;
```

```sql
# 查询商品中,价格大于500的商品信息,包括 商品名称 商品价格 商品所属分类名称
-- 子查询
select p.pname,p.price,c.cname from (select * from products where price > 500) p join category c on p.category_id = c.cid;
-- 内连接查询
select p.pname,p.price,c.cname from products p  join category c on p.category_id = c.cid where p.price > 500;
```

**当子查询作为一张表的时候，需要起别名，否则无法访问表中的字段。**

# 案例1

```sql
# 查询线索(二级渠道jdsc)后续转化成交车型详情。
-- 在二级渠道jdsc下，成交的线索，的车型详情
-- 线索表中二级渠道下的线索 和 订单表中的线索 的 交集。所以使用内连接查询。
select * from clue_day c, order_day o where c.clue_id = o.clue_id and c.ca_n = 'jdsc';
select * from clue_day c join order_day o on c.clue_id = o.clue_id where c.ca_n = 'jdsc';
```

# 案例2

```sql
# 统计所有渠道(按照二级渠道)的转化率
-- 转化率 = 该渠道下订单数/该渠道下线索数。订单数是线索数的子集，所以使用外连接查询。
-- 统计每一个二级渠道的转化率：按二级渠道分组聚合统计
select c.ca_n, count(o.order_id)/count(c.clue_id) 
from clue_day c left join order_day o 
on c.clue_id = o.clue_id 
group by c.ca_n;
```

# 案例3

```sql
# 查询各城市线索数并计算所有城市线索总数
-- 各城市线索数只需要用到clue表一张表，因此不需要多表查询
-- 各城市线索数，需要按城市分组求和
-- 各城市线索数，加起来就是所有城市线索总数，因此是子查询❌
-- 错误原因：子查询无法将两个结果展示在一张表中
-- 使用合并查询，将两个结果拼在一张表中。
-- 所有城市线索总数，也不需要先计算各城市线索数再求和，直接对clue表的线索求和即可。

select c.city_id,count(c.clue_id)clue_num from clue_day c group by c.city_id
union
select '总计',count(c.clue_id) from clue_day c;
```

# 案例4

日期直接相减做了一个隐式转换操作，将时间转换为整数，但并不是用unix_timestamp转换，而是直接把年月日时分秒拼起来，如2013-04-21 16:59:33 直接转换为20130421165933，所以得到的结果是错误的。

Unix_timestamp([日期])函数：返回自'1970-01-01 00:00:00'的到当前日期[指定日期]的秒数差。

Datediff(d1,d2)函数：返回d1-d2的天数差

Ceil()函数：向上取整

```sql
-- 找出优质渠道，做重点投入
-- 我们对于优质渠道(以二级渠道来说)的定义: 
-- 该二级渠道内平均转化周期< 整体平均转化周期
-- 该二级渠道的线索量 > 各二级渠道平均线索量

/*
	针对《二级渠道内平均转化周期》和《二级渠道的线索量》的分析
	转化周期:成单日期 — 线索创建日期 平均:avg
	渠道线索量:分组 count统计
*/
-- 这里需要注意：二级渠道线索量是所有二级渠道的，其中包括未成单的二级渠道。
-- 因此这里使用关联查询时，首先排除内连接，因为内连接只能查出成单的二级渠道
-- 其次使用外连接时，clue表应该作为主表将渠道全部展示出来
select ca_n, avg(datediff(created_at,clue_created_at)), count(c.clue_id) 
from clue_day c left join order_day o on c.clue_id = o.clue_id
where created_at is not null and clue_created_at is not NULL
group by ca_n

-- 二级渠道平均线索量
select ceil(avg(clue_num)) from (
  select ca_n, avg(datediff(created_at,clue_created_at)), count(c.clue_id) 
  from clue_day c left join order_day o 	on c.clue_id = o.clue_id
  where created_at is not null and clue_created_at is not NULL
  group by ca_n
)a -- 子查询作为一张表时，必须有别名才能访问其中的字段

-- 整体平均转化周期
SELECT avg(datediff( created_at, clue_created_at )) 
FROM clue_day c LEFT JOIN order_day o ON c.clue_id = o.clue_id 
WHERE created_at IS NOT NULL AND clue_created_at IS NOT NULL

-- 查询结果
select ca_n, avg_time, clue_num from (
  -- 《二级渠道内平均转化周期》和《二级渠道的线索量》
  select ca_n, avg(datediff(created_at,clue_created_at)), count(c.clue_id) 
  from clue_day c left join order_day o on c.clue_id = o.clue_id
  where created_at is not null and clue_created_at is not NULL
  group by ca_n
)
WHERE avg_time < (
  -- 整体平均转化周期
	SELECT avg(datediff( created_at, clue_created_at )) 
	FROM clue_day c LEFT JOIN order_day o ON c.clue_id = o.clue_id 
	WHERE created_at IS NOT NULL AND clue_created_at IS NOT NULL 
	) 
AND clue_num > (
  -- 二级渠道平均线索量
  select ceil(avg(clue_num)) from (
    select ca_n, avg(datediff(created_at,clue_created_at)), count(c.clue_id) 
    from clue_day c left join order_day o 	on c.clue_id = o.clue_id
    where created_at is not null and clue_created_at is not NULL
    group by ca_n
	)a 
);
```

一段sql查询多次作为子查询被使用，可以用with 别名 as (子查询) 优化：

```sql
-- 《二级渠道内平均转化周期》和《二级渠道的线索量》
with ca_value as (
  select ca_n, avg(datediff(created_at,clue_created_at)), count(c.clue_id) 
  from clue_day c left join order_day o on c.clue_id = o.clue_id
  where created_at is not null and clue_created_at is not NULL
  group by ca_n
)
-- 查询结果
select ca_n, avg_time, clue_num from ca_value
WHERE avg_time < (
	SELECT avg(datediff( created_at, clue_created_at )) 
	FROM clue_day c LEFT JOIN order_day o ON c.clue_id = o.clue_id 
	WHERE created_at IS NOT NULL AND clue_created_at IS NOT NULL 
	) 
AND clue_num > (SELECT ceil(avg( clue_num )) FROM ca_value);
```

**⚠️注意**：[with...as...的用法](https://bbs.csdn.net/topics/390280176)

