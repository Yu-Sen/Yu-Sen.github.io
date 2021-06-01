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

- array类型：array<元素的类型>。数据中每一行array字段的数据，元素个数可以不同，元素类型必须和定义的相同。

- map类型：类似js中的对象，kv键值对。map<key的类型,value的类型>

- struct类型：数据中每一行struct字段的数据，元素个必须相同，元素类型必须和定义的相同。

  比如有一条数据：zss|26,123456,shanghai,695

  建表语句：

  ```
  create table stu_info(
  name string, 
  info struct<age:int,id:string,address:string,score:double>)
  row format delimited 
  fields terminated by '|'
  collection items terminated by ',';
  ```

- uniontype类型：可以理解为泛型，创建表的时候不确定这个字段到底是什么类型的时候可以用联合体uniontype。

# hive内部表和外部表

Hive表分为内部表和外部表

Hive默认建立的表是内部表，内部表create之后，然后load加载hdfs上的数据，会移动物理数据到Hive的数据仓库默认目录（/user/hive/warehouse/xx.db/)下。

内部表drop之后，元数据和物理数据都会删除。

外部表在导入hdfs的数据后，数据并没有移动到自己的数据仓库目录下，也就是说外部表中的数据并不是由它自己来管理的！

外部表drop掉之后，元数据删掉了，但是实际物理数据还存在原位置。

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-28_17-35-31.jpg)

# hive中DDL数据定义语言

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

如果是本地虚拟机创建的服务器，data1.txt上传到服务器本地目录下，在hive目录下load local data...

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

# hive -f执行文件中的sql语句创建数据库

当要创建的数据库、数据表非常多时，通过命令行手动输入语句的方式就很麻烦。

可以将sql语句都写在一个文件中，在hive中通过hive -f '文件路径' 命令直接执行文件中的sql语句。

本地虚拟机环境，在服务器其他目录上写入sql文件，在hive安装目录下，执行`hive -f 'sql文件路径'`即可。

# hive中DML数据操作语言

## 装载数据

### **从本地装载数据**

1. 普通表：load data local inpath '数据文件路径' [overwrite] into table 表名 ; 

   overwrite 关键字表示覆盖原有数据,没有此关键字表示追加数据

   ```
   load data local inpath '/home/hadoop/datas/payments.txt' into table payments;
   ```

2. 分区表：load data local inpath '数据文件路径' [overwrite] into table 表名 partition (分区字段=值);

   **分区表加载数据不支持动态分区**，load的时候必须要写明是哪个分区。

   ```
   load data local inpath '/home/hadoop/datas/product_category_level1.txt' into table product_category partition(level=1); 
   ```

   虽然分区表load时只能静态，但insert时可以动态，所以可以变相的实现分区表动态load数据。实现原理就是，建一个中间表，把数据load到中间表上，再把中间表的数据insert到分区表上，达到动态分区的目的。

   ```
   # 目前有一个分区表，分区字段为A，现在实现向分区表中加载数据同时动态分区
   
   -- 建中间表，中间表不用分区，只用来临时存储数据
   create table 中间表(字段和字段类型与分区表保持一致);
   
   -- load数据到中间表
   load data inpath '...' into table 中间表;
   
   -- 把中间表数据 insert 到分区表中，同时动态分区
   insert overwrite table 分区表 partition(分区字段A) select *,分区值 from 中间表;
   ```

3. 分桶表：load data local inpath '数据文件路径' [overwrite] into table 表名;

   ```
   --开启分桶功能  
   set hive.enforce.bucketing=true  
   -- 忽略掉安全检查  
   hive.strict.checks.bucketing=false;  
   
   load data local inpath '/home/hadoop/datas/shop.txt' into table shops; 
   ```

### 从HDFS装载数据

不写local，其他一样。同理分区表要指定分区，分桶表与普通表加载数据一样。

1. liunx 本地文件上传到HDFS文件系统

   hdfs dfs -put 本地文件路径 hdfs路径

   ```
   #本地路径文件上传到hdfs路径下
   hdfs dfs -put '/home/hadoop/datas/product_info.txt' '/datas' 
   #命令查看文件目录
   hdfs dfs -ls /datas
   ```

2. 文件数据装载到hive表中

   load data inpath 'hdfs数据文件路径' into table 表名;

   ```
   load data inpath '/datas/product_info.txt' into table product_info;
   ```

## 插入数据

1. 普通表：

   追加数据：insert into [table] 表名 values(值)

   重写数据：insert overwrite table 表名 values(值)

   注意 insert overwrite 时 table 关键字不可省略

   ```
   --插入数据
   insert into sales_info(sku_id,sku_name) values(1,'sku_new');
   --查看插入的数据
   insert into sales_info(sku_id,sku_name) select sku_id,sku_name from sales_info;
   ```

2. 分区表：insert into 表名 partition (分区字段=字段值) values()

   ```
   --静态分区
   insert into test_partition1 partition(sku_class="xiaomi") values(1,'sku_new');
   --动态分区
   insert into test_partition1 partition(sku_class) values(1,'sku_new','苹果');
   ```

   静态分区插入一个不存在的分区，也会创建该分区：

   比如表中没有sku_class='xiaomi'这样的分区，使用静态分区插入数据的方式partition(sku_class='xiaomi')，会发现依然能够插入成功，并且添加了sku_class='xiaomi'的分区。

   所以静态分区和动态分区的区别在于：静态分区是通过语句写死的，动态分区是通过数据动态生成的。并不是说静态分区必须通过alter语句提前添加好分区才能插入数据，插入数据时一样可以添加。

3. 分桶表：insert into 分桶表表名 select * from 中间表

   ```
   #插入数据
   insert into test_buckets values(1,'sku_new');
   # 查看插入的中间数据
   select * from test_buckets;
   ```

## 导出数据

### 导出到本地文件系统

insert overwrite local directory '文件夹路径' row format delimited fields terminated by '字段分隔符' select * from 要导出的表;

注意:

overwrite把指定的文件夹重写了 (一定要小心覆盖掉有用的文件) 

默认分隔符是用系统指定的，和本身建表语句指定的没有关系 

有新建文件夹功能

导出的文件都名都为000000_0

```
INSERT OVERWRITE LOCAL DIRECTORY '/home/hadoop/datas/test_output' ROW FORMAT
DELIMITED FIELDS TERMINATED by '\t'
select * from sales_info;
```

### 导出到HDFS

insert overwrite directory '文件夹路径' 查询语句;

```
insert overwrite  directory  '/datas'  select * from sales_info;
```

## 删除

### 删除表中所有数据

#### 删除内部表数据

hive不支持delete，删除内部表数据只能用truncate

```
truncate table 表名
```

#### 删除外部表数据

在linux系统下，使用shell命令删除外部表数据(hdfs dfs -rm -r 外部表路径)

```
hdfs dfs -rm -r /datas/表名称/*
```

### 删除表中部分数据

hive不支持delete，只能通过insert overwrite重写的方式达到删除部分数据的目的。

#### 分区表

1. 删除分区：alter table table_name drop partition(partiton_name='value'))

   ```
   --删除指定分区
   alter table test_partition1 drop partition(sku_class = 'xiaomi');
   ```

2. 删除分区内部分数据：

   ```
   -- 对test_partition_mul表的sku_class='xiaomi',sku_label='dianzi'分区进行重写
   insert overwrite table test_partition_mul partition(sku_class='xiaomi',sku_lable='dianzi')
   select sku_id,sku_name from test_partition_mul
   where sku_id='1235';
   ```

   重新把对应的partition信息写一遍，通过WHERE 来限定需要留下的信息，没有留下的信息就被删 除了。

#### 普通表/分桶表

insert overwrite table 表名 select * from 表名 where 条件;

```
--删除不为2的数据
Insert overwrite table sales_info(sku_id,sku_name) select sku_id,sku_name from sales_info where sku_id='2';
```

### 删除表

drop table 表名

```
drop table test_external;
```

# hive运算符和函数

## 运算符

### 关系运算符

| 运算符                  | 操作         | 描述                                                         |
| ----------------------- | ------------ | ------------------------------------------------------------ |
| A =B                    | 所有基本类型 | 如果表达A等于表达B，结果TRUE ，否则FALSE。                   |
| A != B                  | 所有基本类型 | 如果A不等于表达式B表达返回TRUE ，否则FALSE。                 |
| A <B                    | 所有基本类型 | 如果表达式A小于表达式B为TRUE，否则FALSE。                    |
| A <= B                  | 所有基本类型 | 如果表达式A小于或等于表达式B为TRUE，否则FALSE                |
| A >B                    | 所有基本类型 | 如果表达式A大于表达式B为TRUE，否则FALSE。                    |
| A >= B                  | 所有基本类型 | 如果表达式A大于或等于表达式B为TRUE，否则FALSE。              |
| A [NOT] BETWEEN B AND C | 基本数据类型 | 如果A，B或者C任一为NULL，则结果为NULL。<br />如果A的值大于等于B而且 小于或等于C，则结果为TRUE，反之为FALSE。<br />如果使用NOT关键字则可 达到相反的效果。 |
| A IS [NOT] NULL         | 所有类型     | 如果A等于NULL，则返回TRUE，反之返回FALSE, NOT 正好相反。     |
| A IN(数值 1, 数值2)     | 所有类型     | 如果A存在指定的数据中，则返回TRUE，反之返回FALSE             |
| A [NOT] LIKE B          | 字符串       | 如果A与B匹配的话，则返回TRUE;反之返回FALSE。<br />%代表任意多个字 符，_代表一个字符 |
| A RLIKE B               | 字符串       | 如果A或B为NULL;如果A任何子字符串匹配Java正则表达式B;否则 FALSE。 |
| A REGEXP B              | 字符串       | 等同于RLIKE.                                                 |

### 算术运算符

| 运算符 | 操作         | 描述              |
| ------ | ------------ | ----------------- |
| A +B   | 所有数字类型 | A加B的结果        |
| A -B   | 所有数字类型 | A减去B的结果      |
| A /B   | 所有数字类型 | A除以B的结果      |
| A %B   | 所有数字类型 | A除以B.产生的余数 |

### 逻辑运算符

| 运算符  | 操作    | 描述                                      |
| ------- | ------- | ----------------------------------------- |
| A AND B | boolean | 如果A和B都是TRUE，否则FALSE。             |
| A && B  | boolean | 类似于 A AND B.                           |
| A OR B  | boolean | TRUE，如果A或B或两者都是TRUE，否则FALSE。 |
| A \|\|B | boolean | 类似于 A OR B.                            |
| NOT A   | boolean | TRUE，如果A是FALSE，否则FALSE。           |
| !A      | boolean | 类似于 NOT A.                             |

### 复杂运算符

| 运算符 | 操作                                 | 描述                                        |
| ------ | ------------------------------------ | ------------------------------------------- |
| A[n]   | A是一个数组，n是一个int              | 它返回数组A的第n个元素，第一个元素的索引0。 |
| M[key] | M是一个 Map<K, V>，并且key 的类型为K | 它返回对应于映射中关键字的值。              |
| S.x    | S是一个结构                          | 它返回S的s字段                              |

## 函数

### 数学函数

| 函数                                         | 返回类型            | 描述                                                         |
| -------------------------------------------- | ------------------- | ------------------------------------------------------------ |
| round(double a) <br />round(double a, int d) | BIGINT <br />DOUBLE | 返回double类型的整数值部分 (遵循四舍五入) 返回 指定精度d的double类型 |
| floor(double a)                              | BIGINT              | 返回等于或者小于该double变量的最大的整数                     |
| ceil(double a)                               | BIGINT              | 返回等于或者大于该double变量的最小的整数                     |
| rand()<br /> rand(int seed)                  | DOUBLE              | 返回一个0到1范围内的随机数。如果指定种子seed， 则会得到一个稳定的随机数序列. |
| pow(double a, double p)                      | DOUBLE              | 返回a的p次幂                                                 |
| sqrt(double a)                               | DOUBLE              | 返回a的平方根                                                |
| abs(double a) <br />abs(int a)               | DOUBLE <br />INT    | 返回数值a的绝对值                                            |

### 日期函数

| 函数                                                         | 返回类型 | 描述                                                         |
| ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| from_unixtime(bigint unixtime[, string format])              | STRING   | 转化UNIX时间戳(从1970-01-01 00:00:00 UTC到指 定时间的秒数) 到当前时区的时间格式 |
| unix_timestamp() unix_timestamp(string date) unix_timestamp(string date, string pattern) | BIGINT   | 获得当前时区的UNIX时间戳 转换格式为"yyyy-MM-dd HH:mm:ss"的日期到UNIX时间戳。如果转化失败，则 返回0 转换pattern格式的日期到UNIX时间戳。如果转 化失败，则返回0 |
| to_date(string timestamp)                                    | STRING   | 返回日期时间字段中的日期部分                                 |
| year(string date) <br />month (string date) <br />day (string date) | INT      | 分别返回日期中的年 月 天                                     |
| hour (string date) <br />minute (string date) <br />second (string date) | INT      | 分别返回日期中的时 分 秒                                     |
| weekofyear (string date)                                     | INT      | 返回日期在当年的第几周                                       |
| datediff(string enddate, string startdate)                   | INT      | 返回结束日期减去开始日期的天数 日期有格式要求 yyyy-mm-dd hh:MM:ss 或 yyyy-mm-dd |
| date_add(string startdate, int days)                         | STRING   | days为正数，返回开始日期startdate增加days天后的日期。<br />days为负数，返回开始日期startdate减少days天前的日期。 |
| date_sub (string startdate, int days)                        | STRING   | 返回开始日期startdate减少days天后的日期                      |
| add_months(string startdate, int months)                     | STRING   | months为正数，返回开始日期startdate增加months月后的日期。<br />months为负数，返回开始日期startdate减少months月前的日期。 |

### 条件判断函数

| 函数                                                       | 返回类型 | 描述                                                         |
| ---------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| if(boolean testCondition, T valueTrue, T valueFalseOrNull) | T        | 当条件testCondition为TRUE时，返回 valueTrue;否则返回valueFalseOrNull |
| coalesce(T v1, T v2, ...)                                  | T        | 返回参数中的第一个非空值;如果所有值都为 NULL，那么返回NULL   |
| CASE a WHEN b THEN c [WHEN d THEN e] [ELSE f] END          | T        | 如果a等于b，那么返回c;如果a等于d，那么返 回e;否则返回f       |
| CASE WHEN a THEN b [WHEN c THEN d] [ELSE e] END            | T        | 如果a为TRUE,则返回b;如果c为TRUE，则返回d;否则返回e           |

### 字符串函数

| 函数                                                         | 返回类型 | 描述                                                         |
| ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| length(string A)                                             | INT      | 返回字符串A的长度                                            |
| reverse(string A)                                            | STRING   | 返回字符串A的反转结果                                        |
| concat(string A, string B...)                                | STRING   | 返回输入字符串连接后的结果，支持任意 个输入字符串            |
| concat_ws(string SEP, string A, string B...)                 | STRING   | 返回输入字符串连接后的结果，SEP表示各个字符串间的分隔符      |
| substr(string A, int start)<br />substring(string A, int start) <br />substr(string A, int start, int len)<br />substring(string A, int start, int len) | STRING   | 返回字符串A从start位置到结尾的字符串 返回字符串A从start位置开始，长度为 len的字符串 |
| upper(string A) ucase(string A)                              | STRING   | 返回字符串A的大写格式                                        |
| lower(string A) lcase(string A)                              | STRING   | 返回字符串A的小写格式                                        |
| trim(string A) <br />ltrim(string A)<br />rtrim(string A)    | STRING   | 去除字符串两边的空格 除字符串左边的空格 去除字符串右边的空格 |
| regexp_replace(string A, string B, string C)                 | STRING   | 将字符串A中的符合java正则表达式B的 部分替换为C               |
| regexp_extract(string subject, string pattern, int index)    | STRING   | 将字符串subject按照pattern正则表达式的规则拆分，返回index指定的字符 |
| parse_url(string urlString, string partToExtract [, string keyToExtract]) | STRING   | 返回URL中指定的部分。partToExtract 的有效值为:HOST, PATH, QUERY, REF, PROTOCOL, AUTHORITY, FILE, and USERINFO. |
| get_json_object(string json_string, string path)             | STRING   | 解析json的字符串json_string,返回path 指定的内容。如果输入的json字符串无 效，那么返回NULL |
| space(int n)                                                 | STRING   | 返回长度为n的空格字符串                                      |
| repeat(string str, int n)                                    | STRING   | 返回重复n次后的str字符串                                     |
| lpad(string str, int len, string pad) rpad(string str, int len, string pad) | STRING   | 将str进行用pad进行左补足到len位 将str 进行用pad进行右补足到len位 |
| split(string str, string pat)                                | ARRAY    | 按照pat字符串分割str，会返回分割后的 字符串数组              |
| find_in_set(string str, string strList) find_in_set('ab','aa,ab,ac') | INT      | 返回str在strlist第一次出现的位置， strlist是用逗号分割的字符串。如果没有 找该str字符，则返回0 |
| instr(string str, string substr) instr("abcde","ab")         | INT      | 返回substr在str中第一次出现的位置， 未出现则返回0(如果参数为NULL则返 回NULL;位置从1开始) |

### 统计函数

| 函数                                                         | 返回类型 | 描述                                                         |
| ------------------------------------------------------------ | -------- | ------------------------------------------------------------ |
| count(*)<br />count(expr)<br />count(DISTINCT expr[, expr_.]) | INT      | count(*)统计检索出的行的个数，包括NULL值的行;<br />count(expr)返回指定字段的非空值的个数;<br />count(DISTINCT expr[, expr_.])返回指定字段的不同的非空值的个数 |
| sum(col), sum(DISTINCT col)                                  | DOUBLE   | sum(col)统计结果集中col的相加的结果;sum(DISTINCT col)统计结果中col不同值相加的结果 |
| avg(col), avg(DISTINCT col)                                  | DOUBLE   | avg(col)统计结果集中col的平均值;avg(DISTINCT col)统计 结果中col不同值相加的平均值 |
| min(col) max(col)                                            | DOUBLE   | 统计结果集中col字段的最小值 统计结果集中col字段的最大 值     |
| var_pop(col) var_samp (col)                                  | DOUBLE   | 统计结果集中col非空集合的总体方差 统计结果集中col非空 集合的样本变量 |
| stddev_pop(col) stddev_samp (col)                            | DOUBLE   | 统计结果集中col非空集合的总体标准差 统计结果集中col非 空集合的样本标准差 |
| percentile(BIGINT col, p)                                    | DOUBLE   | 求准确的第p个百分位数，p必须介于0和1之间，但是col字段 目前只支持整数，不支持浮点数类型 |
| percentile(BIGINT col, array(p1 [, p2]...))                  | ARRAY    | 功能和上述类似，之后后面可以输入多个百分位数，返回类 型也为array，其中为对应的百分位数 |

### 复合类型构建访问函数

| 函数                                  | 返回类型 | 描述                                           |
| ------------------------------------- | -------- | ---------------------------------------------- |
| map (key1, value1, key2, value2, ...) | MAP      | 根据输入的key和value对构建map类型              |
| struct(val1, val2, val3, ...)         | STRUCT   | 根据输入的参数构建结构体struct类型             |
| array(val1, val2, ...)                | ARRAY    | 根据输入的参数构建数组array类型                |
| A[n]                                  |          | 返回数组A中的第n个变量值。数组的起始下标 为0。 |
| M[key]                                |          | 返回map类型M中，key值为指定值的value值         |
| S.x                                   |          | 返回结构体S中的x字段                           |
| size(Map<K.V>) size(Array)            | INT      | 返回map类型的长度 返回array类型的长度          |
| explode(map\|array)                   |          | 列变行                                         |
| collect_set ( col)                    | Array    | 对col行变列并 去重                             |
| collect_list ( col)                   | Array    | 对col行变列并 不去重                           |
| map_keys(map)                         | Array    | 取map类型的所有Key                             |
| map_values(map)                       | Array    | 取map类型的所有value                           |
| array_contains(array,obj)             | T        | 判断指定的obj 是否在数组中                     |

# hive中DQL数据查询语言

## Select语句结构

```
SELECT [ALL | DISTINCT] select_expr, select_expr, ... FROM table_reference
[WHERE where_condition]
[GROUP BY col_list]
[HAVING having_condition]
[CLUSTER BY col_list | [DISTRIBUTE BY col_list] [SORT BY col_list]][ORDER BY
col_list]
[LIMIT number];
```

语句的顺序不能错。

## hive中语句执行顺序

可以通过explain关键字查看执行顺序：

```
explain select * from sales_info;
```

```
-- 例如
explain
select basic_info.age, count(1) as renshu 
from test_student
where id >3
group by basic_info.age
having renshu > 1
order by renshu desc
limit 10;
```

hive语句的执行顺序:
 **from -->where --> select --> group by -->聚合函数--> having --> order by -->limit**

除了select的顺序和mysql不一样，其他都一样。

## hive参数介绍

## 严格模式hive.mapred.mode

### 查看当前是否为严格模式

```
set hive.mapred.mode
-- 
hive.mapred.mode = strict
```

### 修改为非严格模式或严格模式

```
set hive.mapred.mode = strict 
set hive.mapred.mode = nostrict
```

严格模式下，这三种情况会报错

- 笛卡尔积不能查询(表关联join不写关联条件)
- order by排序，必须加limit语句
-  读取partitioned table，但没有指定partition

## hive.remove.orderby.in.subquery

Hive 3.0.0以上 hive.remove.orderby.in.subquery为true，subqueries和views中没有limit的Order by将会被optimizer(优化器)移除。

在子查询和查询视图的时候需要设置为false。

## 普通查询

```
select * from sales_info;

select sku_id from sales_info;

select sku_id as id from salse_info;
```

## 查询array

```
-- sales_info表数据
 	sku_id	sku_name	id_array
1	123	华为Mate10	["1235","345"]
2	456	华为Mate30	["89","635"]
3	789	小米5	["452","63"]
4	1235	小米6	["785","36"]
5	4562	OPPO Findx	["7875","3563"]
```

### 返回array字段的某个元素

```
-- 查询数组字段的第一个元素
select id_array[0] from sales_info;

1	1235
2	89
3	452
4	785
5	7875
```

### explode()展开array成一行

```
-- 查询数组字段所有数据，并且将数组展开，每个元素在一行展示
select explode(id_array) from sales_info;

1	1235
2	345
3	89
4	635
5	452
6	63
7	785
8	36
9	7875
10	3563
```

### lateral view测试图搭配explode()输出array字段完整展开结果

```
-- 在explode的基础上，每行数组元素还要展示对应的sku_id, sku_name
select sku_id, sku_name, id_list from sales_info lateral view explode(id_array) ids as id_list;

1 123	华为Mate10	1235
2	123	华为Mate10	345
3	456	华为Mate30	89
4	456	华为Mate30	635
5	789	小米5	452
6	789	小米5	63
7	1235	小米6	785
8	1235	小米6	36
9	4562	OPPO Findx	7875
10	4562	OPPO Findx	3563

语法：select 原表字段1, 原表字段2, 虚拟表字段名 from 原表 lateral view explode(原表数组字段名) 虚拟表名 as 虚拟表字段名。
lateral view explode(id_array)ids相当于一个虚拟表，与原表sales_info笛卡尔积关联。也可以多重使用
select ...from ... lateral view ... lateral view ...
lateral view 测试图。侧视图的意义是配合explode（或者其他的UDTF），一个语句生成把单行数据拆解成多行后的数据结果集。
```

### collect_set(),collect_list()还原展开后的array

```
-- sales_info_explode表，sales_info展开后的结果，并且插入了一条重复数据 789 小米5 63
 	sku_id sku_name	id_list
1	123	华为Mate10	1235
2	123	华为Mate10	345
3	456	华为Mate30	89
4	456	华为Mate30	635
5	789	小米5	452
6	789	小米5	63
7	1235	小米6	785
8	1235	小米6	36
9	4562	OPPO Findx	7875
10	4562	OPPO Findx	3563
11	789	小米5	63
```

```
-- 通过letaral view explode能将数组展开，也可以通过collect_set() collect_list()将展开的数组元素再合并回去

-- collect_set()函数 去重
select sku_id,sku_name, collect_set(id_list) from sales_info_explode group by sku_id,sku_name;
-- 结果
 	sku_id	sku_name	_c2
1	123	华为Mate10	["1235","345"]
2	1235	小米6	["785","36"]
3	456	华为Mate30	["89","635"]
4	4562	OPPO Findx	["7875","3563"]
5	789	小米5	["452","63"] --数据中有两条789 小米5 63，这里去重了

-- collect_list()函数 不去重
select sku_id,sku_name, collect_list(id_list) from sales_info_explode group by sku_id,sku_name;
-- 结果
	sku_id	sku_name	_c2
1	123	华为Mate10	["1235","345"]
2	1235	小米6	["785","36"]
3	456	华为Mate30	["89","635"]
4	4562	OPPO Findx	["7875","3563"]
5	789	小米5	["452","63","63"] -- 没去重
```

### concat_ws()数组转字符串，split()字符串转数组

```
-- concat_ws(分隔字符,字符串1,字符串2...) split(字符串,分隔字符)
select 
	sku_id, sku_name, id_array, 
	concat_ws('_', id_array) as aryToStr, 
	split(concat_ws('_', id_array), '_') as strToAry 
from sales_info;

 	sku_id	sku_name	id_array	arytostr	strtoary
1	123	华为Mate10	["1235","345"]	1235_345	["1235","345"]
2	456	华为Mate30	["89","635"]	89_635	["89","635"]
3	789	小米5	["452","63"]	452_63	["452","63"]
4	1235	小米6	["785","36"]	785_36	["785","36"]
5	4562	OPPO Findx	["7875","3563"]	7875_3563	["7875","3563"]


```

## 查询map

### 返回map字段某个key的value

```
-- mapKeys表数据
 	sku_id	sku_name	state_map
1	123	华为Mate10	{"id":"1111","token":"2222","user_name":"zhangsan1 "}
2	456	华为Mate30	{"id":"1113","token":"2224","user_name":"zhangsan3 "}
3	789	小米5	{"id":"1114","token":"2225","user_name":"zhangsan4 "}
4	1235	小米6	{"id":"1115","token":"2226","user_name":"zhangsan5 "}
5	4562	OPPO Findx	{"id":"1116","token":"2227","user_name":"zhangsan6"}
```

```
select state_map['id'] from mapKeys;

 	_c0
1	1111
2	1113
3	1114
4	1115
5	1116

-- 访问不存在的key，会返回null值
select state_map['user_name'], state_map['name'] from mapkeys;
1	zhangsan1 	NULL
2	zhangsan3 	NULL
3	zhangsan4 	NULL
4	zhangsan5 	NULL
5	zhangsan6	  NULL

-- 用if判断，访问不存在的key，返回指定值而非null
select state_map['user_name'], if(state_map['name'] is null, '无', state_map['name']) from mapkeys;
1 zhangsan1 	无
2	zhangsan3 	无
3	zhangsan4 	无
4	zhangsan5 	无
5	zhangsan6	  无
```

### explode()展开map，每个元素一行，key、value各一列

```
select explode(state_map) from mapKeys;
 
 	key	value
1	id	1111
2	token	2222
3	user_name	zhangsan1 
4	id	1113
5	token	2224
6	user_name	zhangsan3 
7	id	1114
8	token	2225
9	user_name	zhangsan4 
10	id	1115
11	token	2226
12	user_name	zhangsan5 
13	id	1116
14	token	2227
15	user_name	zhangsan6
```

### lateral view测试图搭配explode()输出map字段完整展开结果

```
select 
	sku_id, sku_name,stateKey,stateVal 
from mapkeys lateral view explode(state_map) lt as stateKey, stateVal;
 
 	sku_id	sku_name	statekey	stateval
1	123	华为Mate10	id	1111
2	123	华为Mate10	token	2222
3	123	华为Mate10	user_name	zhangsan1 
4	456	华为Mate30	id	1113
5	456	华为Mate30	token	2224
6	456	华为Mate30	user_name	zhangsan3 
7	789	小米5	id	1114
8	789	小米5	token	2225
9	789	小米5	user_name	zhangsan4 
10	1235	小米6	id	1115
11	1235	小米6	token	2226
12	1235	小米6	user_name	zhangsan5 
13	4562	OPPO Findx	id	1116
14	4562	OPPO Findx	token	2227
15	4562	OPPO Findx	user_name	zhangsan6
```

### map_keys()返回map字段全部key数据

```
select map_keys(state_map) from mapkeys;

1	["id","token","user_name"]
2	["id","token","user_name"]
3	["id","token","user_name"]
4	["id","token","user_name"]
5	["id","token","user_name"]
```

### map_values()返回map字段全部值数据

```
select map_values(state_map) from mapkeys;

1 ["1111","2222","zhangsan1 "]
2	["1113","2224","zhangsan3 "]
3	["1114","2225","zhangsan4 "]
4	["1115","2226","zhangsan5 "]
5	["1116","2227","zhangsan6"]
```

### array_contains()判断map_keys()返回的key数组中是否含有某个key

```
select 
	array_contains(map_keys(state_map),"id"),
	array_contains(map_keys(state_map),'name') 
from mapkeys;

1	true	false
2	true	false
3	true	false
4	true	false
5	true	false
```

## 查询struct类型中某个字段的值

```
-- test_student表中，basic_info字段为struct类型，`basic_info` struct<name:string,age:int>

select basic_info.name from test_student;
```

## where各种使用

