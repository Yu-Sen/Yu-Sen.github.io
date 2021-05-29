---
title: 【数据分析】第四阶段：大数据查询利器Hive（二）
date: 2021-05-27 23:58:33
tags:
- 数据分析
categories:
- 学习笔记
description: hive、hql
---

# Hive简介

Hive是facebook为了解决海量日志数据的统计分析开发的基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供类SQL查询功能。

hive没有存储数据的能力，只有使用数据的能力。

# Hive运行机制

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-20-08.jpg)

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-21-46.jpg)

Hive 通过给用户提供的一系列交互接口，接收到用户的指令(SQL)，使用自己的 Driver， 结合元数据(MetaStore)，将这些指令翻译成 MapReduce，提交到 Hadoop 中执行，最后，将 执行返回的结果输出到用户交互接口。

# HQL和SQL区别

注释只能用`--`。

每个语句必须`;`结尾。

# 数据库

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

### 删除空数据库（没有数据），有数据的数据库这样是删不掉的

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

# HQL数据类型

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-34-53.jpg)

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-35-07.jpg)

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-35-45.jpg)

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-36-07.jpg)

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_00-36-23.jpg)

- array类型：个数可以不同，类型必须相同。array<元素的类型>。

- map类型：类似js中的对象，kv键值对。map<key的类型,value的类型>

- struct类型：个数相同，类型相同

  比如有一条数据：zss 26,123456,shanghai,695

  建表语句：`create table stu_info(name string, info struct<age:int,id:string,address:string,score:double>)`

- uniontype类型：可以理解为泛型，创建表的时候不确定这个字段到底是什么类型的时候可以用联合体uniontype。

# hive内部表和外部表

Hive表分为内部表和外部表

Hive默认建立的表是内部表，内部表create之后，然后load加载hdfs上的数据，会移动物理数据到Hive的数据仓库默认目录（/user/hive/warehouse/xx.db/)下。

内部表drop之后，元数据和物理数据都会删除。

外部表在导入hdfs的数据后，数据并没有移动到自己的数据仓库目录下，也就是说外部表中的数据并不是由它自己来管理的！

外部表drop掉之后，元数据删掉了，但是实际物理数据还存在原位置。

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_17-35-31.jpg)

# 数据表

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-29_21-51-20.jpg)

```
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name -- external 指定创建外部表，不指定默认创建内部表
[(col_name data_type [COMMENT col_comment], ...)] -- comment 字段备注
[COMMENT table_comment] -- comment 表的备注
[PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] -- partitioned by 对表进行分区，分区的列名不能存在于表的列名中
[CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 
-- clustered by 对表进行分桶 sorted by排序 buckets桶的个数
[ROW FORMAT row_format] -- row format 设置表的行分隔符、列分隔符，设置分隔符的 
[STORED AS file_format] -- stored as 序列化存储
[LOCATION hdfs_path] -- location 指定外部表的存储位置
```

## 创建数据表

### 创建内部表

```
create table test_create_table(id int, name string);

show create table test_create_table;
LOCATION
	  'hdfs://hadoop1:9000/apps/bigdata/hive/warehouse/lagou.db/test_create_table'
```

### 创建外部表

```
-- 不使用location指定存储位置时，存储位置和内部表的默认存储位置一致
create external table test_external(id int, name string);

show create table test_external;
LOCATION
	  'hdfs://hadoop1:9000/apps/bigdata/hive/warehouse/lagou.db/test_external'
```

```
create external table test_external_1(id int,name string) 
location '/users/HI18011401591/test_external_1';

show create table test_external_1;
LOCATION
	  'hdfs://hadoop1:9000/users/HI18011401591/test_external_1'
```

### like复制表结构创建表

仅复制结构

```
create table test_like like test_create_table;
```

### create table as select查询建表

复制结构和数据

```
create table test_as_select as select * from test_create_table;
```

### comment创建带注释的表

```
create table test_com(id int comment '学生ID', name string comment '学生姓名') comment '学生表'；
```

## 查看数据表列表

```
show tables;
```

## 查看表结构

```
desc test_create_table;
```

## 查看表创建语句

```
show create table test_create_table;

CREATE TABLE `test_create_table`(
	  `id` int, 
	  `name` string)
	ROW FORMAT SERDE 
	  'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe' 
	STORED AS INPUTFORMAT 
	  'org.apache.hadoop.mapred.TextInputFormat' 
	OUTPUTFORMAT 
	  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
	LOCATION
	  'hdfs://hadoop1:9000/apps/bigdata/hive/warehouse/lagou.db/test_create_table'
	TBLPROPERTIES (
	  'bucketing_version'='2', 
	  'transient_lastDdlTime'='1622201439')
```

## insert插入数据

```
insert into test_create_table values (1,'test');

insert into test_external_1 values(1,'test');
```

# 分隔符-创建表

和把csv、txt等文本类型的数据导入至mySql、excel中一样，需要设定分隔符来解析文本中的数据。

hive中默认的分隔符有很多，主要介绍三个：

- ^A：每个列之间的默认间隔符
- ^B：数组中每个元素的默认间隔符
- ^C：map中key与value的默认间隔符

这些都是默认使用的间隔符，我们在创建表的时候可以指定自己要使用的间隔符。

## 设置分隔符

```
-- 语句
row format delimited
```

```
create table 表名(字段1，字段2) row format delimited ...
```

## 查看表分隔符设置

```
desc formatted 表名
```

## 指定列分隔符

```
-- 语句
fileds terminated by '要使用的间隔符'
```

```
create table test_delimited(id int,name string) row format delimited fields terminated by '^B';

desc formatted test_delimited;
--
field.delim         	^B                              
```

接下来如果要通过文件load数据，文件中数据的字段之间应该用'^B'来间隔。

## 指定数组元素分隔符

```
-- 语句
collection items terminated by '要使用的间隔符'
```

```
create table sales_info(
	sku_id string comment '商品id',
	sku_name string comment '商品名称',
	id_array array<string> comment '商品相关id列表' -- id_array字段为数组类型
)
-- 设置分隔符这一部分是一整块，不要加,分隔
row format delimited
fields terminated by '|' -- 列用|分隔
collection items terminated by ','; -- 数组中元素用,分隔

desc formatted sales_info;
--
collection.delim    	,                   
field.delim         	|   
```

建表的时候如果没有数组类型字段，依然可以设置数组元素分隔符。设置和你有有没有这个数据类型、有没有数据没关系。就好比我修改一个文件夹名称，和你文件夹里有没有文件没关系。

```
-- 没有数组类型字段，和设置数组元素间隔符没关系。
create table sales_info2(
    sku_id string,
    sku_name string)
row format delimited
fields terminated by '|'
collection items terminated by ',';

desc formatted sales_info;
--
collection.delim    	,                   
field.delim         	|    
```

这样load文本数据时，文本数据中，列就需要用｜来间隔，数组元素需要用，来间隔。

### 导入数据，看看设置是否有效

这里使用的是hue云平台，在本地准备好数据文件上传到云服务器上。

准备一个数据文件data1.txt，数据内容如下：

```
123|华为Mate10|1235,345 
456|华为Mate30|89,635 
789|小米5|452,63 
1235|小米6|785,36 
4562|OPPO Findx|7875,3563
```

上传data1.txt数据文件到hue云服务器的/user/Hl18011401591路径下

load data语句导入数据

```
load data inpath '/user/Hl18011401591/data1.txt' overwrite into table sales_info;
```

如果数据放在本地目录，不是hdfs目录，语句是load local data...

overwrite是重写，由于表中没有数据，所以加不加overwrite都没影响

查看导入结果

```
select * from sales_info;
--
	sales_info.sku_id	sales_info.sku_name	sales_info.id_array
1	123	华为Mate10	["1235","345"]
2	456	华为Mate30	["89","635"]
3	789	小米5	["452","63"]
4	1235	小米6	["785","36"]
5	4562	OPPO Findx	["7875","3563"]
```

## 指定map键值分隔符

```
-- 语句
map keys terminated by '要使用的间隔符'
```

```
create table mapKeys(
    sku_id string comment '商品id',
    sku_name string comment '商品名称',
    state_map map<string,string> comment '商品状态信息'
    )
row format delimited
fields terminated by '|'
collection items terminated by ',' -- collection也可以指定map中元素的间隔符
map keys terminated by ':';

desc formatted mapKeys;
--
collection.delim    	,                   
field.delim         	|                   
mapkey.delim        	:       
```

### 导入数据测试一下

准备数据文件data2.txt

```
123|华为Mate10|id:1111,token:2222,user_name:zhangsan1 
456|华为Mate30|id:1113,token:2224,user_name:zhangsan3 
789|小米5|id:1114,token:2225,user_name:zhangsan4 
1235|小米6|id:1115,token:2226,user_name:zhangsan5 
4562|OPPO Findx|id:1116,token:2227,user_name:zhangsan6
```

上传data2.txt到hue云服务器/user/Hl18011401591/路径下

load data语句导入数据到mapKeys表中

```
load data inpath '/user/Hl18011401591/data2.txt' into table mapKeys;
```

查看导入结果

```
select * from mapKeys;
-- 
 	mapkeys.sku_id	mapkeys.sku_name	mapkeys.state_map
1	123	华为Mate10	{"id":"1111","token":"2222","user_name":"zhangsan1 "}
2	456	华为Mate30	{"id":"1113","token":"2224","user_name":"zhangsan3 "}
3	789	小米5	{"id":"1114","token":"2225","user_name":"zhangsan4 "}
4	1235	小米6	{"id":"1115","token":"2226","user_name":"zhangsan5 "}
5	4562	OPPO Findx	{"id":"1116","token":"2227","user_name":"zhangsan6"}
```

## 行分隔符默认换行不能修改

hive行分隔符默认是'\n'换行，且不能修改。

```
lines terminated by '\n'
```

## 指定分隔符为多个字符

前面指定分隔符的语句都只能使用一个字符作为分隔符。

如果数据中有有一段文本，文本中本来就有, : |这样的字符，那为了避免把数据内容也当成分隔符，就需要用多个字符来作为分隔符解析数据。

方法一：**MultiDelimitSerDe**语句

```
CREATE TABLE test_MultiDelimit(id int, name string ,tel string)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe' WITH SERDEPROPERTIES ("field.delim"="##")  -- 指定##为列分隔符
STORED AS TEXTFILE;
```

方法二：**RegexSerDe**语句

```
CREATE TABLE test1(id string, name string ,tel string)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe' WITH SERDEPROPERTIES 
("input.regex" = "^(.*)\\#\\#(.*)$")
STORED AS TEXTFILE;
```

```
(id string, name string ,tel string)
-- RegexSerDe语句是用正则来检测分隔符，就要求字段的类型必须都是字符串类型，否则会报错
```

# 分区

mysql中想要查找某个满足某个条件的数据，通过where条件全表扫描，最后select查询出数据。

在hive中数据量巨大，显然不能像mysql中那样为了获取某个条件下的数据而全表扫描。因此就有了分区这个概念。

分区就是按分区字段和字段值把一张大表分成多个小表，提高查询效率。

正常create数据表，是在hive相应的数据库目录下，新建一个名为数据表名称的目录，表中的数据以文件形式存储在该目录中。

而create partitionde by分区表，是在数据表的目录下，再创建名为”分区字段=字段值“的目录，不同分区的数据以文件形式存储在各自的分区目录下。多字段分区的分区目录是层级形式的。

## 分区的意义

- 避免hive全表扫描，提升查询效率。 从而引进分区技术，使用分区技术，避免hive全表扫描，提升查询效率。

- 减少数据冗余进而提高特定(指定分区)查询分析的效率。 
- 在逻辑上分区表与未分区表没有区别，在物理上分区表会将数据按照分区键的列值存储在表目录的子目录中，目录名为“分区键=键值”。

- 查询时尽量利用分区字段。如果不使用分区字段，就会全部扫描。

## 分区的类型

- 静态分区：静态分区是手动指定。比如一个商品表中，分类字段下有手机、冰箱。我在建表的时候手动设置了手机分区表、冰箱分区表。
- 动态分区：动态分区是通过数据来进行判断。还是商品表中，分类字段下有手机，就自动建一个手机分区，有冰箱就自动建一个冰箱，有衣服，就自动建一个衣服分区。

## 创建分区

```
-- 语句
partitioned by(字段名 字段类型)
```

```
-- 创建分区表。这里只是创建了分区表，还没有添加分区。
create table test_partition(
	sku_id string comment '商品id',
	sku_name string comment '商品名称'
)
partitioned by (sku_class string); -- 分区的字段一定不要出现在表中，不然就重复冗余了。
```

## 添加静态分区

创建好分区表后，静态分区需要alter设置好分区字段值。

```
-- 添加静态分区。静态分区要先设置好分区，再插入数据
alter table test_partition add partition(sku_class='xiaomi'); -- 添加sku_class字段等于xiaomi的分区
```

### 查看分区

```
show partitions test_partition;
--
 	partition
1	sku_class=xiaomi
```

### 静态分区添加数据

#### insert添加

```
-- partition(分区字段=值)指定数据插入的分区
insert into table test_partition partition(sku_class='xiaomi') values(1,'test');
```

#### 导入数据文件添加

```
--本地文件加载
load data local inpath '本地文件路径' into table test_partition1 partition (sku_class='xiaomi');
```

#### select查看数据是否添加到分区表中

非严格模式下，可以直接select全表扫描查询

```
select * from test_partition;
-- 
 	test_partition.sku_id	test_partition.sku_name	test_partition.sku_class
1	1	test	xiaomi
```

严格模式下，为了避免全表扫描，必须用where过滤分区字段。否则报错。

```
select * from test_partition where sku_class = 'xiaomi';
```

严格模式/非严格模式设置：

```
set hive.mapred.mode = strict; -- 严格
set hive.mapred.mode = nonstrict; -- 非严格
```

## 添加动态分区

创建好分区表后，动态分区不需要alter设置分区，直接在insert插入数据时指定分区字段，就可以动态建立分区。

动态分区hive默认是不开启的，需要先设置一下才能使用动态分区，否则报错。

```
set hive.exec.dynamic.partition = true;
set hive.exec.dynamic.partition.mode = nonstrict;
```

### insert插入数据同时动态生成分区

```
insert into table test_partition partition(sku_class) values(2, 'test2', 'huawei');
```

### 查看分区

```
show partitions test_partition;
--
 	partition
1	sku_class=huawei
2	sku_class=xiaomi
```

### select查看数据是否添加成功

```
select * from test_partition;
--
 	test_partition.sku_id	test_partition.sku_name	test_partition.sku_class
1	2	test2	huawei
2	1	test	xiaomi
```

## 多字段分区

### 创建分区表

创建分区表的时候，partitioned by(字段1 类型, 字段2 类型...)

```
create table test_partition_mu(
sku_id string comment '商品id',
sku_name string comment '商品名称'
)
partitioned by(sku_class string, sku_label string);
```

### 添加静态分区

添加分区的时候，paritition(字段1=值, 字段2=值...)

```
alter table test_partition_mu add if not exists partition(sku_class='xiaomi', sku_label='dianzi');
```

if not exists判断，如果不存在这样的分区就执行添加。存在的话就算了。

#### 查看分区

```
show partitions test_partition_mu;
--
 	partition
1	sku_class=xiaomi/sku_label=dianzi
```

#### insert插入数据

```
insert into table test_partition_mu partition(sku_class='xiaomi',sku_label='dianzi') values(1,'test');

select * from test_partition_mu;
--
test_partition_mu.sku_id	test_partition_mu.sku_name	test_partition_mu.sku_class	test_partition_mu.sku_label
1	1	test	xiaomi	dianzi
```

#### 导入数据文件添加数据

```
--加载本地文件到分区表中
load data local inpath '本地文件路径' into table test_partition partition (sku_class='xiaomi',sku_lable='dianzi');
```

### 添加动态分区

```
-- partition(分区字段1, 分区字段2...) values(....分区字段1值,分区字段2值...)
insert into table test_partition_mu partition(sku_class,sku_label) values(2,'test','tesla','qiche');
```

#### 查看分区

```
show partitions test_partiton_mu;
--
partition
1	sku_class=tesla/sku_label=qiche
2	sku_class=xiaomi/sku_label=dianzi
```

#### 查看数据

```
select * from test_partition_mu;
--
 	test_partition_mu.sku_id	test_partition_mu.sku_name	test_partition_mu.sku_class	test_partition_mu.sku_label
1	2	test	tesla	qiche
2	1	test	xiaomi	dianzi
```

## 删除分区

alter table 表名 drop partition(分区字段=值)

```
alter table test_partition drop partition(sku_class='xiaomi');
```

分区目录和分区目录下的数据文件都被删除。

# 分桶

分桶相比分区，就是更细粒度的分割。

分桶可以对表分割，也可以对分区分割。

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-29_22-02-03.jpg)

## 创建分桶

```
-- 语句
clustered by(分桶字段) [sorted by(排序字段)] into 桶个数 buckets
```

```
create table test_buckets(
sku_id string comment '商品id',
sku_name string comment '商品名称')
clustered by(sku_id) into 3 buckets;
```

## 开启自动分桶

添加数据前要确保自动分桶已经开启

```
set hive.enforce.bucketing = true;
```

## 添加数据

```
insert into test_buckets select sku_id,sku_name from sales_info;
```

在test_buckets表目录下，按照分桶数生成了3个数据文件，每个文件存储着随机分割的数据。

由于分桶是随机分割的，所以分桶的作用主要是随机抽样，和提高join查询效率。

