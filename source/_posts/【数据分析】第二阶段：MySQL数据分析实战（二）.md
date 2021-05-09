---
title: 【数据分析】第二阶段：MySQL数据分析实战（二）
date: 2021-05-08 22:46:42
tags:
- 数据分析
categories:
- 学习笔记
description: 窗口函数、索引、视图
---

# 1. 窗口函数

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

## 1.1 聚合类窗口函数

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
# 错误原因：group by只是完成了按月分组，必须还要完成select month(pay_time), sum(pay_amount)才能获取最终的数据集，如果在select中加入窗口函数，此时并没有取得数据集，窗口函数也就无法执行。所以需要子查询的方式，先让group by完成select取得数据集，再将数据集作为表通过子查询执行窗口函数

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

## 1.2 排序窗口函数

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

Ntile(n) over(partition by <分组字段A> order by <排序字段B>)函数简单的说，就是对将个分组（没有分组就是整张表）的数据，先按照order by字段排序，然后分成n组，然后按照排序给每个组排名。

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

## 小结

- 聚合窗口函数 不能 在第一次select时同时执行，必须先select完毕，结果集再作为新表通过子查询使用聚合窗口函数。

```sql
# 报错❌
select month(pay_time) as _month, sum(pay_amount) over(order by month(pay_time))
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

- 排序窗口函数 可以 在第一次select时同时执行。

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
```

## 1.3 偏移分析函数

### lag、lead

lag() 函数，允许您向上偏移多行并从当前行访问偏移行的数据

lead() 函数，允许您向下看多行并从当前行访问偏移行的数据。

```
# 向上偏移
lag(<expression>[,offset[, default_value]]) over (partition by expr,... order by expr [asc|desc],...) 
# 向下偏移
lead(<expression>[,offset[, default_value]]) over (partition by expr,... order by expr [asc|desc],...) 
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