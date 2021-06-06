---
title: 【数据分析】第二阶段：MySQL数据分析实战（二）
date: 2021-05-08 22:46:42
tags:
- 数据分析
categories:
- 学习笔记
description: 窗口函数、索引、视图
---

# 窗口函数

窗口函数（也叫分析函数），从MySQL8.0开始支持。

窗口在数据中限定出一个范围，函数对这个范围内的集合进行统计。

```
函数名() over(子句)
```

over就是窗口，通过子句限定窗口的范围，函数在该范围内执行。

over中的子句具体如下：

```
函数名() over(partition by 分组字段1,分组字段2... order by <排序字段> rows between <范围起始行数> and <范围结束行数>)
```

**⚠️注意：**

- **子句间*没有*逗号，间隔**
- **over子句中多个字段用逗号，分割，不是用and**

具体示例：

```
# 选出D1行至D2行的记录集合，在该集合内，按照B分组，按照C排序，最后每一组对A求和
sum(...A...) over(partition by ...B... order by ...C... rows between ...D1... and ...D2...) 
avg(...A...) over(partition by ...B... order by ...C... rows between ...D1... and ...D2...)

A: 需要被加工的字段名称 
B: 分组的字段名称
C: 排序的字段名称
D: 计算的行数范围
```

```
rows between 2 preceding(在..之前) and current row # 范围：当前查询位置的前2行——当前行，共3行
rows between unbounded(无限) preceding(在..之前) and current row # 范围：当前查询位置的前所有行——当前行
rows between current row and unbounded(无限) following(在...之后) # 范围：当前查询位置的当前行——之后的所有行
rows between 3 preceding and current row # 范围：当前查询位置的前3行——当前行，共4行
rows between 3 preceding and 1 following # 范围：当前查询位置的前3行——后1行，共5行
```

- 当不指定rows between时，窗口规范默认是：该partition分组中rows between unbounded preceding and current row
- 当不指定order by 和 rows between时, 窗口规范默认是：该partition分组中rows between unbounded preceding and unbounded following
- 当不指定partition时，order by的作用范围是整个数据表；指定partition时，order by的作用范围是当前分组
- 当over子句中什么都不写时，窗口规范默认是整张表全部数据

**⚠️注意：where优先级>over，where是对源数据进行过滤，过滤后的结果集再执行over或group by等操作。**

## 聚合类窗口函数

- sum()
- count()
- avg()
- Max()
- min()

窗口函数下的聚合函数 和 普通场景下的聚合函数区别：

普通场景下的聚合函数是将多条记录聚合为一条**(多到一)**；窗口函数下是每条记录都会执 行，有几条记录执行完还是几条**(多到多)**。

### 累计计算

```sql
# 需求1：查询出2019年每月的支付总额和当年累积支付总额
select a._month, a._pay_mount_sum, sum(a._pay_mount_sum) over(order by a._month)
from
(
	select month(pay_time) as _month, sum(pay_amount) as _pay_mount_sum
	from user_trade
	where year(pay_time) = 2019
	group by month(pay_time)
)a

# ❌错误写法
select month(pay_time), sum(pay_amount)，sum(sum(pay_amount)) over(month(pay_time))
from user_trade
where year(pay_time) = 2019
group by month(pay_time)
# 这么写的想法：group by分组已经取得了月份、每个月支付总额，那么再直接用窗口函数就能取得每个月的累计支付总额
# 错误原因：group by只是完成了按月分组，必须还要完成select month(pay_time), sum(pay_amount)才能获取最终的数据集，如果在 select中加入窗口函数，此时并没有取得数据集，窗口函数也就无法执行。所以需要子查询的方式，先让group by完成select取得数据集，再将数 据集作为表通过子查询执行窗口函数

# 需求2：查询出2018-2019年每月的支付总额和当年累积支付总额
select a._year,a._month,sum(a.pay_amount_sum) over(partition by a._year order by a._month)
from
(
	select year(pay_time) _year, month(pay_time) _month, sum(pay_amount) pay_amount_sum
	from user_trade
	group by year(pay_time), month(pay_time)
	having _year in (2018,2019)
	order by _year, _month
)a

# 需求3: 查询出2019年每个月的近三月移动平均支付金额
select a._month, a._pay_amount, avg(a._pay_amount) over(rows between 2 preceding and current row)
from (
	select month(pay_time) _month, sum(pay_amount) _pay_amount
	from user_trade
	where year(pay_time) = 2019
	group by _month
	order by _month
)a
```

## 排序窗口函数

### rank、dense_rank、row_number

- rank() over(partition by <分组字段> order by <排序字段>)，返回排序后的排名（并列排名占用名次）
- Dense_rank() over(partition by <分组字段> order by <排序字段>)，返回排序后的排名（并列排名不占用名次）
- Row_number() over(partition by <分组字段> order by <排序字段>)，返回排序后的行号（从1开始）

⚠️这三种排序函数不需要参数。

```
# 语法
row_number(无参数) over(......) 
rank(无参数) over(......) 
dense_rank(无参数) over(......)

在rank()函数，如果有并列情况，会占用下一个名次的位置，比如，成绩为100的学生有三个并列第一，那么99分的学生是第二名，通过rank()函数，名次是：1,1,1,4；

在dense()函数中，如果有并列的情况，不会占用下一个名词，同用上个例子，名次是：1,1,1,2；

在row_number()函数中，会忽略并列的情况，同用上述例子，名次是：1,2,3,4;
```

```sql
# 需求5: 2020年1月，购买商品品类数的用户排名
# 本案例中 不需要先子查询再对子查询结果使用排序函数。要考虑两个问题，一是所使用的函数是在生成数据集的同时就能使用，还是要生成完毕才可以使用；二是在使用函数的时，是否已经生成了数据集。
SELECT
	user_name,
	-- 这里使用distinct去重，因为一个用户可能在不同的时间购买了同一个品类的商品
	count( DISTINCT goods_category ) goods_c_count,
	row_number() over ( ORDER BY count( DISTINCT goods_category ) ) _row_number,
	rank() over ( ORDER BY count( DISTINCT goods_category ) ) _rank,
	dense_rank() over ( ORDER BY count( DISTINCT goods_category ) ) _dense_rank 
FROM
	user_trade 
WHERE
	YEAR ( pay_time ) = 2020 
	AND MONTH ( pay_time ) = 1 
GROUP BY
	user_name
```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-09_18-26-26.jpg)

### ntile

- ntile(n) over(partition by <分组字段A> order by <排序字段B>)

**n**:切分的片数
**A**:分组的字段名称
**B**:排序的字段名称

ntile(**n**)，用于将分组数据按照顺序切分成n片，返回当前切片值。

Ntile(n) over(partition by <分组字段A> order by <排序字段B>)函数简单的说，就是对各分组（没有分组就是整张表）的数据，先按照order by字段排序，然后分成n组，然后按照排序给每个组排名。

```sql
# 需求6: 查询出将2020年2月的支付用户，按照支付金额分成5组后的结果
select user_name, sum(pay_amount) pay_amount, ntile(5) over(order by sum(pay_amount) desc)level
from user_trade
where year(pay_time) = 2020
and month(pay_time) = 2
group by user_name
```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-09_21-42-58.jpg)

```sql
# 需求7：查询出2020年支付金额排名前30%的所有用户
select a.user_name, a.pay_amount, a.level
from (
	select user_name, sum(pay_amount)pay_amount, ntile(10) over(order by sum(pay_amount) desc) level
	from user_trade
	where year(pay_time) = 2020
	group by user_name
)a
where a.level < 4
```

## 偏移分析函数

### lag、lead

lag() 函数，允许您向上偏移多行并从当前行访问偏移行的数据

lead() 函数，允许您向下看多行并从当前行访问偏移行的数据。

```
# 向上偏移
lag(<expression>[,offset[, default_value]]) over (partition by expr... order by expr [asc|desc]...) 
# 向下偏移
lead(<expression>[,offset[, default_value]]) over (partition by expr... order by expr [asc|desc]...) 
```

执行到当前分组的某一行时，返回偏移offset行的行的expression字段的值，如果偏移量超过了分组或窗口范围，返回default_value。

如果不写offset参数，默认偏移1行。

如果不写default_value参数，默认值为null。

```sql
SELECT user_name,pay_time,
       lag(pay_time,1,pay_time) over(partition by user_name order by pay_time) lag1,
			-- 没有传入偏移量，那么默认就是1，找不到的话，此处也没有给默认值，为 null
			 lag(pay_time) over(partition by user_name order by pay_time)lag1_s,
       lag(pay_time,2,pay_time) over(partition by user_name order by pay_time) lag2,
       lag(pay_time,2) over(partition by user_name order by pay_time)lag2_s
FROM user_trade
WHERE user_name in ('King','West');
```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-09_22-54-39.jpg)

**在实际应用当中，若要用到取今天和昨天的某字段差值时，Lag和Lead函数的应用就显得尤为重要。**

```sql
# 需求10: 查询出支付时间间隔超过100天的用户数
select count(distinct a.user_name)
from (
	select 
  	user_name,pay_time, 
  	lead(pay_time,1) over(partition by user_name order by pay_time) lead_time 
	from user_trade
)a
where a.lead_time is not null
and datediff(a.lead_time,a.pay_time) > 100
```

```sql
# 需求11: 查询出每年支付时间间隔最长的用户
# ✅
select b.user_name, year(b.pay_time), b.diff_time
from (
		select 
			a.user_name, a.pay_time, 
			a.lead_time, datediff(a.lead_time,a.pay_time) diff_time, 
			rank() over(partition by year(a.pay_time) order by datediff(a.lead_time,a.pay_time) desc) _rank
		from (
			select user_name, pay_time, lead(pay_time,1) over(partition by user_name, year(pay_time) order by pay_time) lead_time
			from user_trade
		)a
		where a.lead_time is not null
	)b
	where b._rank = 1

# ❌，错误原因：
/*
在第2层子查询计算出了每年每个用户的支付间隔后，外套一层查询，并按年分组，然后用max（）选出每组，也就是每年间隔时间最长的人，
这时发现，在select中只有max(b.diff_time)时不会报错，但加上b.user_name就会报错。
这时因为，一年中存在多个用户的支付时间间隔并列最长，这时max(b.diff_time)会默认返回第一个(还是随机一个），但是user_name有多个，系统不知道该返回哪个，就会报错了。
所以这里不能用max，应该用rank()函数，这样就考虑到了并列最大的情况。
*/
select b.user_name, max(b.diff_time)
from (
	select a.user_name, a.pay_time, a.lead_time, datediff(a.lead_time,a.pay_time) diff_time
	from (
		select user_name, pay_time, lead(pay_time,1) over(partition by user_name, year(pay_time) order by pay_time) lead_time
		from user_trade
	)a
	where a.lead_time is not null
)b
group by year(b.pay_time)
```

**max()并列最大错误测试：**

测试表数据

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-10_00-07-31.jpg)

只select max()不报错

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-10_00-08-38.jpg)

select user_id报错

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-10_00-09-15.jpg)

## 小结

- 聚合窗口函数 不能 在第一次select时同时执行，必须先select完毕，结果集再作为新表通过子查询使用聚合窗口函数。

```sql
# 报错❌
select 
	month(pay_time) as _month, 
	sum(pay_amount) over(order by month(pay_time))
from user_trade
where year(pay_time) = 2019
group by month(pay_time)

# 正确✅
select a._month, a._pay_mount_sum, sum(a._pay_amount_sum) over(order by a._month)
from (
	select month(pay_time) as _month, sum(pay_amount) as _pay_amount_sum
	from user_trade
	where year(pay_time) = 2019
	group by month(pay_time)
)a
```

- 排序窗口函数、偏移分析函数 可以 在第一次select时同时执行。

```sql
# ✅
SELECT
	user_name,
	count( DISTINCT goods_category ) goods_c_count,
	row_number() over ( ORDER BY count( DISTINCT goods_category ) ) _row_number,
	rank() over ( ORDER BY count( DISTINCT goods_category ) ) _rank,
	dense_rank() over ( ORDER BY count( DISTINCT goods_category ) ) _dense_rank 
FROM
	user_trade 
WHERE
	YEAR ( pay_time ) = 2020 
	AND MONTH ( pay_time ) = 1 
GROUP BY
	user_name
	
# ✅
select 
 	user_name,pay_time, 
 	lead(pay_time,1) over(partition by user_name order by pay_time) lead_time 
from user_trade
```

# 索引

字典的目录就是一种索引机制，提高检索的效率。

- 索引可以提升查询速度，会影响where和order by。
- 索引是针对字段的，需要添加到字段上。给某一列添加索引的时候，数据库会将这一列的数据进行提取、转换、再存储。存储下来的数据就是索引。当查询的时候，会先查询索引数据，能够更快速的找到相关信息。
- 索引在大量数据场景下效果明显。

## 常见索引分类

- 从索引的存储结构划分:B Tree索引、Hash索引、fulltext全文索引、R Tree索引(了解) 

- **从应用层次划分:主键索引、唯一索引、普通索引、复合索引** 

- 从索引的键值(字段)类型划分:主键索引、辅助索引(二级索引) 

- 从索引数据和内容数据逻辑关系划分:聚集索引(聚簇索引)、非聚集索引(非聚簇索 引)

## 主键索引(primary key)

- 数据表添加主键的时候，会自动穿件逐渐索引，主键索引也是一种唯一索引。
- 一个表可以没有主见，但最多只能有一个主键，且主键是唯一的、不能为Null。
- 根据主键进行where条件查询，效率高。

## 唯一索引(unique)

特点：索引列的值只能出现一次，不能重复，保证唯一。

在许多场合，人们创建唯一索引不是为了提高查询速度，而是为了避免数据重复。（使用唯一约束效果相同）

```sql
# 创建表的时候直接添加唯一索引
CREATE TABLE 表名( 
  列名 类型(长度),
	UNIQUE [索引名称] (列名)
);

# 在已有的表上创建索引
create unique index 索引名 on 表名(列名[长度])
create unique index index_dname on test_index(dname);

# 修改表结构添加索引
ALTER TABLE 表名 ADD UNIQUE 索引名( 列名 )
```

## 普通索引(normal index)

普通索引的唯一任务是加快检索的速度。一般为需要where条件查询或order by排序的列添加普通索引。

普通索引不要求数据唯一、非空。

```sql
# 在已有的表上创建索引
create index 索引名 on 表名(列名[长度])

# 修改表结构添加索引
alter table 表名 add index 索引名 (列名)
```

## 复合索引

创建普通索引时，同时写入多个字段。

复合索引一般用在，需要对多个字段进行where条件查询或order by排序。

```sql
# 在已有的表上创建索引
create index 索引名 on 表名(列名1, 列名2,...)

# 修改表结构添加索引
alter table 表名 add index 索引名 (列名1， 列名2,...)
```

⚠️注意：创建复合索引时要注意字段顺序，创建索引时字段的顺序，要和查询条件中字段的顺序保持一致。

```sql
where 字段1=‘’ and 字段2=‘’	-- 字段1，字段2

create index 索引名 on 表名(字段1, 字段2) -- 字段1，字段2；不能写成 字段2，字段1
```

⚠️注意：区别复合索引和多个单列索引。

```sql
# 复合索引
create index 索引名 on 表名(列名1, 列名2) -- 字段顺序要和查询条件中保持一致

# 多个单列索引
create index 索引名 on 表名(列名1) -- 不需要考虑字段顺序
create index 索引名 on 表名(列名2)
```

## 删除索引

```sql
alter table 表名 drop index 索引名;
```

# 视图

概念：

- 视图是一种虚拟表。（与之对应的是实体表，比如通过create语句创建的表）
- 视图建立在已有表的基础上, 视图赖以建立的这些表称为基表。
-  向视图提供数据内容的语句为 SELECT 语句, 可以将视图理解为存储起来的 SELECT 语句。
- 视图向用户提供基表（实体表或者其他视图）数据的另一种表现形式。

```sql
create view 视图名 [column_list] as select语句;
view: 表示视图
column_list: 可选参数，表示属性清单，指定视图中各个属性的名称，默认情况下，与 SELECT语句中查询的属性相同
as : 表示视图要执行的操作
select语句: 向视图提供数据内容
```

作用：

- 权限控制时可以使用
- 简化复杂的多表查询

sql中视图的概念类似于编程中封装函数的概念，将一段复杂的sql语句封装成一个视图，之后只需通过引用视图名进行查询就能得到结果。并且可以决定从视图中只查询某几个字段的数据，做到权限控制，就像函数可以指定返回的值一样。

```sql
# 需求1: 查询各个分类下的商品平均价格
create view cp_view as
select *
from category c
join products p
on c.cid = p.category_id; -- 注意这里必须有分号;结束

select cname, avg(price)
from cp_view
group by cname

# 普通写法
select cname, avg(price)
from products p left join category c
on p.category_id = c.cid
group by c.cname
```

```sql
# 需求2: 查询鞋服分类下最贵的商品的全部信息
create view cp_view2 as 
select c.cid, c.cname, p.pid,p.pname, p.price, p.flag, rank() over(partition by c.cname order by p.price desc) _rank
from products p left join category c 
on p.category_id = c.cid;

select * from cp_view2 where cname = '鞋服' and _rank = 1

# 普通写法
select *
from (
select c.cid, c.cname, p.pid,p.pname, p.price, p.flag, rank() over(partition by c.cname order by p.price desc) _rank
from products p left join category c 
on p.category_id = c.cid 
)a
where a.cname = '鞋服' and a._rank = 1
```

视图和表的区别：

- 视图是建立在表的基础上，表存储数据库中的数据，而视图只是做一个数据的展示 。
- 通过视图不能改变表中数据(一般情况下视图中的数据都是表中的列 经过计算得到的结果, 不允许更新)。
-  删除视图，表不受影响，而删除表，视图不再起作用。

# 作业——知识点

## 留存

留存：指基准日到APP的用户在之后的n日当天返回APP的人数。

留存率 = 基准日之后的n天当日返回的用户数 / 基准日的用户数 * 100%。



留存代表一个用户愿意再次使用你的产品;而一个产品能够被用户再次使用，意味着这个产品是能够满

足用户长期需求的，能够让用户产生一定粘性的产品。



活跃用户 = 新增用户 + 留存用户



如今互联网产品大多为免费产品，依靠持续的广告转化、用户持续的购买转化、用户持续的会员付费来维持收益。

持续的广告转化、购买、会员付费，我们就 需要每天都有一定量的用户来完成以上过程，也就是说我们需要我们的产品每天都是有活跃用户的。

从活跃用户的构成，我们就知道怎么维持我们的活跃用户规模：一方面持续有新用户流入，另一方 面持续有留存用户留存。



为什么留存用户重要?部分有过从业经验的同学应该知道，产品的自然新增用户是比较少的，如果 我们仅靠产品自然用户增长，那么必然我们的活跃用户量级会非常小，虽然能够获得收益，但是这部分 收益很可能养不起我们的公司;但是如果我们想要用购买新用户的方式获得持续的较大规模的用户池， 又会产生比较大的成本，这样做容易亏本。所以我们需要新增留存两手抓;想方设法留住我们新增的用 户，让其长期地在我们的产品内产生价值，这样做，比起单做新增，在成本和效果上都是要好的。



**留存的主要应用场景有两个：**

- 一个是产品整体视角的持续留存情况;也就是我们需要定期分析用户在产品的留存是否维持在一个正常 的范围，有没有突然地涨跌情况，如果有的话问题出在哪里，为什么会出现这样的问题，如何解决留存 异常的问题。遇到这类问题首先需要确定的是整体用户的留存异常还是个别群体用户的留存异常，如果 是整体用户的留存异常，我们需要分析我们的产品是否发生异常，可以通过用户的行为路径确定异常产 品位置;如果是个别用户的留存异常，需要通过用户拆解的方法来定位有异常的用户群体，然后通过这 类用户的特征来分析问题原因。

- 一个是新增用户视角的每批新增用户留存情况。一般出现在计算投放ROI的场景，也就是我们投放一批 用户，能够给我们带来多少收益。我们会用这些用户的“整个生命周期能够带来的收入”(LTV)去除以 “投放时的支出”来计算我们的ROI。“整个生命周期能够带来的收入”计算的是引入一批新用户，当用户完 全流失时，这部分用户所带来的所有收入。但是有时我们需要提前预估新用户在整个生命周期能够带来 的收入，我们就需要首先用留存来估计用户的生命周期，然后用估计出的生命周期再去计算用户的整个 生命周期能够带来的收入。

**什么企业不关心留存：** 

需要用户产生持续活跃，持续转化，持续消费的企业都关心用户的留存。那么，什么样的企业不关心用户留存?

- 部分线下企业存在数据难以获取的问题，所以对留存关注比较少，比如线下的商场，除非用户产生购买 行为，否则很难探知用户在什么情况下有回流。

- 部分存在一锤子买卖的企业也不太关心留存，比如说用户在一次买断商品后再也不会产生后续购买或价 值转化行为，这类企业也不怎么关注留存。比如PC单机游戏;比如一次付费的工具类产品(线上线下类 工具都存在这个情况)。

## 周同比

周同比：周同比适用于以周为周期变动的数据，用于观察每天的数据较上周同一天的数据变化。

现有互联网数据多以周为周期变动，一周内每一天的数据都有其特点:

- 如工作类APP，周一会是每一周的高峰，然后向周五逐渐递减，周五到周六又会有一个锐减的过 程，周日与周六趋于平缓;

- 如娱乐类APP，周一会较周末锐减，然后向周四逐渐递减，周五会有一个跃升的过程，周六会再次 跃升，周日会较周六下降;

一般遇到这样的变动趋势，如果单纯的分析每一天较上一天的变动情况，就容易分析出错误结论。所 以，在我们分析带有周期类数据时，一定要先将周期提炼出来，使用周期的同比数据做分析，会得出更 有价值，更加准确的结论。

## 平台模式

如今的大多数内容APP都是以平台模式运营的。

什么叫做平台模式?

比如我们现在能接触到的大多数内容产品“抖音”、“Bilibili”、“微博”、“今日头条”等等。我们使用到的这个 APP，是由APP的制作公司提供的，也就是这个APP会有几个主要的页面，每个页面怎么引导用户进入内 容页面，播放内容的方式有哪些、内容间切换会用什么方式等等，都是我们的APP制作公司决定的;而 我们在这个APP里看到的大多数内容，都是由内容作者提供的。



