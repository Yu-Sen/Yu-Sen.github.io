---
title: 【数据分析】第四阶段：大数据查询利器Hive（二）下
date: 2021-06-02 15:34:07
tags:
- 数据分析
categories:
- 学习笔记
description: hive运算符和函数，hive中DQL
---

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

## explain查看hive中语句执行顺序

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

hive语句是转化为mapReduce语句执行的。 

Map过程：from，where，select。根据每一个map产生一个本地的reduce，也对本地的数据块进行group by，聚合，但并不进行having。

Reduce过程：再次进行group by，聚合。然后进行having，order by，limit等。 把map产生的reduce进行合并，合并为我们指定的reduce个数。reduce会产生文件输出。

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

```
-- sales_info表数据
 	sku_id sku_name	id_array
1	123	华为Mate10	["1235","345"]
2	456	华为Mate30	["89","635"]
3	789	小米5	["452","63"]
4	1235	小米6	["785","36"]
5	4562	OPPO Findx	["7875","3563"]
```

### like,rlike,instr()字符串中是否包含某个字符

```
-- 选出sku_name包含A的
select * from sales_info where sku_name like '%A%';

select * from sales_info where instr(sku_name,'A')>0;
-- instr(字符串,字符) 返回字符在字符串中首次出现的位置，如果没有返回0

select * from sales_info where sku_name rlike 'A';
-- rlike 正则表达式。这里用的java正则，和js正则在转义符上不同
在其他语言中，\\ 表示：我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义。
在 Java 中，\\ 表示：我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义。
-- js中 \d 表示 0-9
-- java中 \\d 表示 0-9
```

### in(),or,find_in_set()值是否存在某个集合中

```
-- sku_name为小米5或小米6的
select * from sales_info where sku_name in('小米5','小米6');

select * from sales_info where sku_name='小米5' or sku_name='小米6';

select * from sales_info where find_in_set(sku_name,'小米5,小米6')>0;
-- find_in_set(字符串,字符串集) 返回字符串在字符串集中首次出现的位置，没有返回0；字符串集是多个字符串用,间隔的字符串
```

### is null,size()=-1基本类型、数组类型是否为null

```
-- 基本类型是否为null
select * from sales_info where sku_id is null;

-- 数组类型是否为null
select * from sales_info where size(id_array)= -1;
--size(数组) 返回数组长度，也就是数组元素个数。当值为null时，返回-1
size(array('')) = 0
size(array('1')) = 1
size(null) = -1
```

### between and, >= and <=值是否在某个范围内

```
select * from sales_info where sku_id between 100 and 800;

select * from sales_info where sku_id>=100 and sku_id<=800;
```

### array_contains(),find_in_set()数组中是否含有某个元素

```
-- id_array中含有89这个元素的
select * from sales_info where find_in_set('89',concat_ws(',',id_array))>0;

select * from sales_info where array_contains(id_array,'89');
```

### concat_ws()数组中是否有元素含有某个字符

```
-- id_array中有元素含有8这个字符
select * from sales_info where concat_ws('',id_array) like '%8%';
select * from sales_info where concat_ws('',id_array) rlike '8';
```

### array_contains()map中是否含有某个key

```
-- mapkeys表中，state_map列含有'id'这个key的
select * from mapkeys where array_contains(map_keys(state_map),'id');
```

### not, ! 取反

```
-- mapkeys表中，state_map列 不 含有'id'这个key的
select * from mapkeys where not array_contains(map_keys(state_map),'id');
select * from mapkeys where ! array_contains(map_keys(state_map),'id');
```

### map中某个key的值包含某个字符

```
-- mapkeys表中，state_map列，user_name(key)的值包含'zhang'的
select * from mapkeys from state_map['user_name'] rlike 'zhang';
```

## group by和聚合函数

### having和where

与mysql一样，having时对group by的过滤

### group by用法

与mysql一样，select后面只能写group by的字段和聚合函数。对于字符串类型的数据，可以用字符串函数聚合，比如collect_set()等。

### 以struct、map类型数据中的某个key分组

```
-- basic_info字段为struct类型，basic_info struct<user_name:string, age:int>
select basic_info.age,count(*) from test_student
group by basic_info.age
having count(*) > 1;

-- state_map字段为map类型，state_map map<string,string>
select state_map['id'],count(sku_id) from mapkeys
group by state_map['id']
```

### 以array类型数据中是否含有某个值分组

```
-- id_array字段为array类型，以id_array是否有89这个值分组，有的分一组并统计记录数，没有的分一组并统计记录数
select array_contains(id_array,'89'),count(*) 
from sales_info 
group by array_contains(id_array,'89');
```

### 以分区表各分区分组

```
-- test_partition表为分区表，sku_class为分区字段
-- 按分区分组，统计每个分区的记录数，选出记录数大于1的分区
select sku_class, count(*) num
from test_partition
group by sku_class
having num > 1;
```

### 统计order_data表，每年下半年的订单量

```
-- 先按年分组，再对分组后的每组数据having筛选月份大于6的
select
	year(order_data),
	sum(订单量)
from order_data 
group by year(order_date) 
having month(order_date)>6;
-- 先where筛选月份大于6的数据，再对过滤出的数据按年分组
select
	year(order_data),
	sum(订单量)
from order_data
where month(order_date)>6
group by year(order_date);
```

## limit

和mysql用法相同

```
# 查询emp表中的前5条数据
-- 参数1:起始值,默认是0; 参数2:要查询的条数 
SELECT * FROM emp LIMIT 5;
SELECT * FROM emp LIMIT 0 , 5;
# 查询emp表中 从第4条开始,查询6条 -- 起始值默认是从0开始的.
SELECT * FROM emp LIMIT 3 , 6;
```

## hive中排序介绍

分区表、分桶表排序，和普通表没有差异。分区表、分桶表虽然物理上数据文件被分到了不同的目录、分成了不同的文件，但逻辑上还是一整张表。

hive中有几种排序方式

|      | order by                               | sort by                | distribute by                           | cluster by                       |
| ---- | -------------------------------------- | ---------------------- | --------------------------------------- | -------------------------------- |
| 作用 | order by对输入做全局排序               | 单独在各自reduce中排序 | 控制map中的输出在reduce中如何划分       | 相当于distribute by和sort by合用 |
| 缺点 | 只有一个reduce，输入规模较大时耗时较长 | 不能保证全局有序       | 只负责划分，不能排序，要和sort by一起用 | 只能升序                         |

```
-- 查看当前reduce设置为几个，默认为-1无限制
set mapred.reduce.tasks
-- mapred.reduce.tasks=-1

-- 设置reduce个数
set mapred.reduce.tasks = 2

1个reduce是产生一个文件的
```

## order by排序

```
-- 单列排序
select * from tableName order by 字段1;

-- 多列排序
select * from tableName order by 字段1,字段2;

-- 别名排序，因为hive中select的执行顺序在order by之前，所以order by可以使用select时字段的别名
select 字段1, 字段2 as 字段2别名 from tableName order by 字段2别名；

-- 和limit搭配使用
select * from tableName order by 字段1 limit 2,3; -- 从第3条开始取3条，就是3、4、5这三条

-- order by默认asc升序，
-- 排序字段中有null时，默认是nulls first将null排在第一位，可以改为nulls last将null排在最后
select * from tableName order by 字段1 nulls last;
```

- order by

不管我们设置几个reduce，order by总是用一个reduce输出，来保证全局排序。

```
-- 设置reduce为2
set mapred.reduce.tasks = 2;

-- 导出数据，reduce设为2，路径下会有2个数据文件。(由于表中数据量太小，这里用group by，让reduce按着设置值生成2个文件)
insert overwrite directory '/user/Hl1591/test_reduce2/' 
select sku_id from sales_info group by sku_id;

-- 加上order by，发现只生成一个数据文件
insert overwrite directory '/user/Hl1591/test_reduce2_orderBy/'
select sku_id from sales_info group by sku_id
order by sku_id;
```

## sort by排序

sort by只对各reduce内数据排序，全局不排序。

但当我们数据量足够小只生成了1个reduce，或reduce指定为1个的时候，sort by就相当于全局排序了。

## distribute by划分reduce

distribute by只有划分reduce功能，没有排序功能。如果要排序，需要和sort by连用。

举例：

```
-- 设置reduce为6个
set mapred.reduce.tasks=6;

-- order_data表中，order_date字段为日期类型，且只有2018和2019年的数据
-- 现在对order_data表，以order_date的年份使用distribute by划分reduce
-- 并且使用sort by 按 sales销售额字段降序排序
select * from order_data
distribute by year(order_date)
sort by sales desc;

-- 把查询结构导出成文件，看看reduce为6个，且使用了distribute by，数据文件时如何划分的
insert overwrite directory '/user/Hl1591/test_distribute'
row format delimited
fields ternimated by '\t'
select * from order_data
distribute by year(order_date)
sort by sales desc;

在test_distribute目录下，可以看到由于reduce被设为6，生成了6个数据文件。
但其中只有两个文件是有数据的，其他文件都没有数据。
这两个数据文件，一个全部是2018年的数据，另一个全部是2019年的数据。
并且，两个文件各自按照sales字段降序排序。
```

对分区表、分桶表使用distribute by，和普通表完全一样。

## cluster by

当distribute by和sort by使用同一个字段，且升序排序时，可以简写为cluster by。

```
select * from order_data distribute by quantity sort by quantity;
-- 等于
select * from order_data cluster by quantity;

通过将查询结果导出成数据文件，发现两种写法结果完全一致。
```

# 表关联查询

hive中关联查询和mysql基本一样，只有以下几点区别：

- hive只支持等值连接。即hive中不支持join on A != B
- on后面的表达式不支持or

## inner join内连接

和mysql一样，inner可省略，起别名时as可省略

也可以select from a,b where a.xx = b.xx

## left join左连接，right join右连接

和mysql一样。

```
select * from 
a left join b
on a.xx = b.xx
where a.xx=XX and b.xx!=XX;

join和where一起使用，先将表通过on条件join在一起，再对合并后的结果集where过滤。
```

## full join全连接

mysql中不支持full outer join，但可以通过 左连接union右连接实现。

full join就是数据横向的维度的并集。

```
select * from
a full join b
on a.xx = b.xx;
```

# 结果集的合并

## union,union all并集

和mysql一样，union去重按查询顺序排序，union all不去重不排序

union，union all是数据纵向的记录的并集。

```
select * from a
union
select * from b;
```

## intersect交集

取两个结果集的交集，去重，排序

```
select * from a
intersect
select * from b;
```

## minus差集

取两个结果集的差集（第一个减去第二个），去重，排序

```
select * from a minus select * from b; -- a-b，得出a有b没有的

select * from b minus select * from a; -- b-a，得出b有a没有的
```

# 视图

和mysql的视图基本一样的。

```
CREATE VIEW [IF NOT EXISTS] [db_name.]view_name -- 视图名称 
[(column_name [COMMENT column_comment], ...) ] --列名 
[COMMENT view_comment] --视图注释
[TBLPROPERTIES (property_name = property_value, ...)] --额外信息 
AS SELECT ...;

-- 不写其他参数
create view 视图名 as
select ...;
```

视图还可以通过视图创建。

```
create view view2 as
select * from view1;
```

查看视图：

```
-- 查看视图是否创建成功
show tables;

-- 查看视图
desc 视图名;

-- 查看视图详细信息
desc formatted 视图名;
```

删除视图

```
drop view [if exists] 视图名;
```

修改视图

```
alter view 视图名 as
selec...
```

在修改指定列的视图后，指定的列名失效 

当基表的数据记录增删时，视图也会生变化 

当基表删除视图引用的列后，视图会失效 

当基表添加列后，视图还是原有的列，对新列不做引用。

 当基表或基视图被删除后，此视图失效

- 视图是只读的，不能用作 LOAD / INSERT / ALTER 的目标; 

- 在创建视图时候视图就已经固定，对基表的增加列操作将不会反映在视图，删除视图引用的列，视图会失效;
- 删除基表并不会删除视图，需要手动删除视图;
- 视图可能包含 ORDER BY 和 LIMIT 子句。如果引用视图的查询语句也包含这类子句，其执行优先 级低于视图对应字句
- 创建视图时，如果未提供列名，则将从 SELECT 语句中自动派生列名;
- 创建视图时，如果 SELECT 语句中包含其他表达式，例如 x + y，则列名称将以*C0* C1 等形式生成;

# 窗口函数

和mysql中的窗口函数基本一样。

## 聚合窗口函数

```
-- 和mysql一样
sum/max/min/avg/count(字段名) over(partition by 分组字段名 order by 排序字段名 rows between and )

rows between: unbounded,preceding,followning,current row
```

## 偏移窗口函数

lag()，lead()和mysql一样

hive多了个两个：first_value()，last_value()

```
-- 取分组内，排序后，截止到当前行，第一个值
first_value(col,default) over(......)

-- 取分组内，排序后，截止到当前行，最后一个值
last_value(col,default) over(......)
```

## 排序窗口函数

row_number()，rank()，dense_rank()，ntile()和mysql一样

nitle()是等频切片，比如有20条数据，ntile(5)，每片就是4条数据。

hive多了两个：cume_dist()，percent_rank()

- cume_dist()：小于等于当前值的行数/分组内总行数
- percent_rank()：分组内当前行的RANK值-1/分组内总行数-1

# hive中的子查询

hive 3.1 支持select，from，where 子句中的子查询 

select 子查询限制：不支持 if / case when 里的子查询 

where 子查询限制：

​	IN/NOT IN 子查询只能选择一列

​	EXISTS/NOT EXISTS 必须有一个或多个相关谓词

​	对父查询的引用仅在子查询的WHERE子句中支持（mysql中支持子查询的其他地方引用父查询）

集合中如果含null数据，不可使用not in, 可以使用in 

主查询和子查询可以不是同一张表



```
-- 正常运行
select 
	user_info.customer_id,
	(select 
			count(distinct order_id) 
		from test_join_order 
		where user_info.customer_id = test_join_order.customer_id ) as count 
from user_info
where (
	select 
		count(distinct order_id) 
	from test_join_order 
	where user_info.customer_id = test_join_order.customer_id )>0;

-- 报错，父查询where那里使用了子查询的别名count，因为hive执行顺序from-where-select
select 
	user_info.customer_id,
	(select 
			count(distinct order_id) 
		from test_join_order 
		where user_info.customer_id = test_join_order.customer_id ) as count 
from user_info
where count>0;
```

# 抽样查询

在海量数据下进行数据分析任务时，如果对全量数据进行分析会非常耗时耗费资源，因此一般情况下抽取一小部分数据进行分析建模。

当然数据越多，分析肯定越准确，在条件允许的情况下，全量数据分析肯定更好。

hive中三种抽样方式：

## 随机抽样 rand()函数

使用rand()函数，与distribute by，sort by，order by结合进行随机抽样。

原理就是通过distribute by，sort by，order by对rand()生成的随机数排序，最后通过limit取部分数据达到随机抽样的效果。

```
select * from order_data order by rand() limit 30;

select * from order_data distribute by rand() sort by rand() limit 30;
```

## 数据块抽样 tablesample()函数

### 用法一：tablesample(n percent)

tablesampe(n percent)根据数据文件的大小按百分比抽样。由于hive使用hdfs存储数据文件，hdfs中默认一个数据块大小是128M，所以当数据文件本身小于128M时，使用tablesample无论设置n为多少百分比，都会抽取出全部数据。

可以简单理解为，在hive中，数据文件大小小于128M时，tablesample(n percen)函数抽样不起作用，会抽出全部数据。

```
--select语句不能带where条件且不支持子查询
select * from order_data tablesample(10 percent) --抽取数据文件大小10%的数据
```

### 用法二：tablesample(nM)

和percent一样，还是按照数据文件大小抽样，只不过不是按百分比，而是按具体的多少M(兆)抽样。也存在数据大小小于hive默认数据块大小128M时，不起作用抽出全部数据的问题。

```
select * from order_data tablesample(1M) --抽取1M大小的数据
```

### 用法三：tablesample(n rows)

指定抽样数据的行数，其中n代表每个map任务均取n行数据。

如果现在有3个mapreduce，虽然n设置为1，但是取得的行数是3。

```
select * from order_data tablesample(1 rows) --每个map任务抽取1行
```

## 分桶抽样

语法：tablesample(bucket x out of y) 。从第x个桶开始取，取总桶数/y个桶。

y必须是table总bucket数的倍数或者因子。hive根据y的大小，决定抽样的比例

- 未分桶表

  ```
  -- 未分桶表使用tablesample(bucket x out of y)时，
  -- y就是总桶数，后面跟 on 字段名 对表以某个字段分桶
  -- x依旧表示从第几个桶开始取
  -- 案例里on rand()表示不按某个字段分桶，按rand()产生的随机数分桶，y是10，就是分10个桶
  select * from order_data1 tablesample(bucket 1 out of 10 on rand())
  ```

- 分桶表

  ```
  -- test_bucket分了3个桶
  select * from test_bucket 
  tablesample(bucket 1 out of 6 on sku_id) -- 从第1个桶开始取，取3/6=0.5个桶的数据
  
  --test_bucket分了6个桶
  select * from test_bucket 
  tablesample(bucket 1 out of 3 on sku_id) -- 从第1个桶开始取，取6/3=2个桶的数据，第二个桶是 1+3
  ```

# 自定义函数

自定义函数分为三个类别:

UDF(User Defined Function):一进一出(upper(), lower())

UDAF(User Defined Aggregation Function):聚集函数，多进一出(例如count/max/min)

UDTF(User Defined Table Generating Function):一进多出，如lateral view explode()

hive中创建自定义函数 需要用java来编写，而不是用传统的SQL来完成

# hive语句优化

## group by代替distinct去重

```
select  distinct customer_id
from test_join_order;

select  customer_id
from test_join_order;
group by customer_id
```

## grouping sets()函数

grouping sets是一种将多个group by 逻辑写在一个sql语句中的便利写法。

```
-- 假设表中有a,b,num三个字段，现在要按照a，b，a,b三种方式分组统计num总和，并将结果集合并
SELECT a,b,sum(num) AS total_num
  FROM DW_AAA.BBB
 GROUP BY a,b
 UNION ALL
SELECT a,sum(num) AS total_num
  FROM DW_AAA.BBB
 GROUP BY a
 UNION ALL 
SELECT b,sum(num) AS total_num
  FROM DW_AAA.BBB
 GROUP BY b
 
 -- 等价于
SELECT a
      ,b
      ,sum(num) AS total_num
  FROM DW_AAA.BBB
 GROUP BY a,b
 GROUPING SETS (a,b),(a),(b)
```

## grouping sets()函数（转）

GROUPING SETS作为GROUP BY的子句，允许开发人员在GROUP BY语句后面指定多个统计选项，可以简单理解为多条group by语句通过union all把查询结果聚合起来结合起来。

几个demo帮助大家了解：

| grouping sets语句                                            | 等价hive语句                                                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| select device_id,os_id,app_id,count(user_id) from  test_xinyan_reg group by device_id,os_id,app_id grouping sets((device_id)) | SELECT device_id,null,null,count(user_id) FROM test_xinyan_reg group by device_id |
| select device_id,os_id,app_id,count(user_id) from  test_xinyan_reg group by device_id,os_id,app_id grouping sets((device_id,os_id)) | SELECT device_id,os_id,null,count(user_id) FROM test_xinyan_reg group by device_id,os_id |
| select device_id,os_id,app_id,count(user_id) from  test_xinyan_reg group by device_id,os_id,app_id grouping sets((device_id,os_id),(device_id)) | SELECT device_id,os_id,null,count(user_id) FROM test_xinyan_reg group by device_id,os_id <br/>UNION ALL <br/>SELECT device_id,null,null,count(user_id) FROM test_xinyan_reg group by device_id |
| select device_id,os_id,app_id,count(user_id) from  test_xinyan_reg group by device_id,os_id,app_id grouping sets((device_id),(os_id),(device_id,os_id),()) | SELECT device_id,null,null,count(user_id) FROM test_xinyan_reg group by device_id <br/>UNION ALL <br/>SELECT null,os_id,null,count(user_id) FROM test_xinyan_reg group by os_id <br/>UNION ALL <br/>SELECT device_id,os_id,null,count(user_id) FROM test_xinyan_reg group by device_id,os_id  <br/>UNION ALL <br/>SELECT null,null,null,count(user_id) FROM test_xinyan_reg |

## CUBE函数

cube简称数据魔方，可以实现hive多个任意维度的查询，cube(a,b,c)则首先会对(a,b,c)进行group by，然后依次是(a,b),(a,c),(a),(b,c),(b),(c),最后在对全表进行group by，他会统计所选列中值的所有组合的聚合

```
select device_id,os_id,app_id,client_version,from_id,count(user_id) 
from test_xinyan_reg 
group by device_id,os_id,app_id,client_version,from_id with cube;
```

手工实现需要写的hql语句（写个程序自己生成的，手写累死）：

```
SELECT device_id,null,null,null,null ,count(user_id) FROM test_xinyan_reg group by device_id
UNION ALL
SELECT null,os_id,null,null,null ,count(user_id) FROM test_xinyan_reg group by os_id
UNION ALL
SELECT device_id,os_id,null,null,null ,count(user_id) FROM test_xinyan_reg group by device_id,os_id
UNION ALL
SELECT null,null,app_id,null,null ,count(user_id) FROM test_xinyan_reg group by app_id
UNION ALL
SELECT device_id,null,app_id,null,null ,count(user_id) FROM test_xinyan_reg group by device_id,app_id
UNION ALL
SELECT null,os_id,app_id,null,null ,count(user_id) FROM test_xinyan_reg group by os_id,app_id
UNION ALL
SELECT device_id,os_id,app_id,null,null ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,app_id
UNION ALL
SELECT null,null,null,client_version,null ,count(user_id) FROM test_xinyan_reg group by client_version
UNION ALL
SELECT device_id,null,null,client_version,null ,count(user_id) FROM test_xinyan_reg group by device_id,client_version
UNION ALL
SELECT null,os_id,null,client_version,null ,count(user_id) FROM test_xinyan_reg group by os_id,client_version
UNION ALL
SELECT device_id,os_id,null,client_version,null ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,client_version
UNION ALL
SELECT null,null,app_id,client_version,null ,count(user_id) FROM test_xinyan_reg group by app_id,client_version
UNION ALL
SELECT device_id,null,app_id,client_version,null ,count(user_id) FROM test_xinyan_reg group by device_id,app_id,client_version
UNION ALL
SELECT null,os_id,app_id,client_version,null ,count(user_id) FROM test_xinyan_reg group by os_id,app_id,client_version
UNION ALL
SELECT device_id,os_id,app_id,client_version,null ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,app_id,client_version
UNION ALL
SELECT null,null,null,null,from_id ,count(user_id) FROM test_xinyan_reg group by from_id
UNION ALL
SELECT device_id,null,null,null,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,from_id
UNION ALL
SELECT null,os_id,null,null,from_id ,count(user_id) FROM test_xinyan_reg group by os_id,from_id
UNION ALL
SELECT device_id,os_id,null,null,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,from_id
UNION ALL
SELECT null,null,app_id,null,from_id ,count(user_id) FROM test_xinyan_reg group by app_id,from_id
UNION ALL
SELECT device_id,null,app_id,null,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,app_id,from_id
UNION ALL
SELECT null,os_id,app_id,null,from_id ,count(user_id) FROM test_xinyan_reg group by os_id,app_id,from_id
UNION ALL
SELECT device_id,os_id,app_id,null,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,app_id,from_id
UNION ALL
SELECT null,null,null,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by client_version,from_id
UNION ALL
SELECT device_id,null,null,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,client_version,from_id
UNION ALL
SELECT null,os_id,null,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by os_id,client_version,from_id
UNION ALL
SELECT device_id,os_id,null,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,client_version,from_id
UNION ALL
SELECT null,null,app_id,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by app_id,client_version,from_id
UNION ALL
SELECT device_id,null,app_id,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,app_id,client_version,from_id
UNION ALL
SELECT null,os_id,app_id,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by os_id,app_id,client_version,from_id
UNION ALL
SELECT device_id,os_id,app_id,client_version,from_id ,count(user_id) FROM test_xinyan_reg group by device_id,os_id,app_id,client_version,from_id
UNION ALL
SELECT null,null,null,null,null ,count(user_id) FROM test_xinyan_reg
```


看着很蛋疼是不是，体会到cube的强大了吗！(低版本hive可以通过union all方式解决，算是没有办法的办法)

## ROLL UP函数

rollup可以实现从右到左递减多级的统计，显示统计某一层次结构的聚合。

```
select device_id,os_id,app_id,client_version,from_id,count(user_id) 
from test_xinyan_reg 
group by device_id,os_id,app_id,client_version,from_id with rollup;
```

等价以下sql语句：

```
select device_id,os_id,app_id,client_version,from_id,count(user_id) 
from test_xinyan_reg 
group by device_id,os_id,app_id,client_version,from_id 
grouping sets ((device_id,os_id,app_id,client_version,from_id),(device_id,os_id,app_id,client_version),(device_id,os_id,app_id),(device_id,os_id),(device_id),());
```

————————————————
版权声明：本文为CSDN博主「扫大街的程序员」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/moon_yang_bj/article/details/17200367

