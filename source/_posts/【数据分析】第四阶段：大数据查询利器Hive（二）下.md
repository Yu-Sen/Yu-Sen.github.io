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

与mysql一样，select后面只能写group by的字段和聚合函数。

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

