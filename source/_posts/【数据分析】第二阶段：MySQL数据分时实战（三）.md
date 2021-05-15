---
title: 【数据分析】第二阶段：MySQL数据分时实战（三）
date: 2021-05-11 11:14:17
tags:
- 数据分析
categories:
- 学习笔记
description: MySQL项目实战
---

# 案例一 基于**MySQL**的电商用户、商品、平台价值分析

电商行业，从早年粗放式经营，到现在精细化运营。随平台数据量积累，通过数据分析挖掘消费者潜在需求。

人-货-场 指标体系。人-用户，货-商品，场-平台。

探索用户行为规律，寻找高价值用户（消费频率高，消费金额高）；分析商品特征，寻找高贡献商品（成交量大，利润高）；分析产品功能，优化产品路径。

## 确认问题

通过对用户行为数据进行分析，解决以下问题：

- 基于漏斗模型的用户购买流程各环节分析指标，确定各个环节的转换率，便于找到需要改进的 环节。（优化产品）
- 找出热销商品，研究热销商品特点。（找到热门产品）
- 基于RFM模型找出核心付费用户群，对这部分用户进行精准营销。（找到高价值用户）

## 搭建指标

用户-商品-平台

### 用户指标

- 基础指标：uv、pv、dv（浏览深度）、留存率
- RFM模型

#### UV

unique vistor，一定时间内访问网站的用户数总和。

```sql
select 
	dates,
	-- 统计每一天的用户数总和，使用count对user_id计数，其中可能存在一个用户有多次记录，因此去重
	count(distinct user_id) uv,
	count(if(behavior_type = 1, user_id, null)) pv, 
	count(if(behavior_type = 1, user_id, null)) / count(distinct user_id) dv
from temp_trade
group by dates -- 案例中要求计算每日pv、uv、dv，因此按日期分组
```

#### PV

page view，一定时间内所有用户每次访问网站的页数总和。代表网站的用户粘性非常高，PV高网站的跳出率就低，说明网站内容更被用户喜欢。

```sql
select 
	dates,
	count(distinct user_id) uv, 
	-- 统计每天每个用户浏览网站的页数的总和，同一个用户在一天不同时间段多次浏览网页也被计算在内，因此不需要去重。
	-- 本案例中一条behavior_type=1（曝光）的记录视为用户浏览了一个页面，因此对一天中曝光的user_id计数。
	count(if(behavior_type = 1, user_id, null)) pv, 
	count(if(behavior_type = 1, user_id, null)) / count(distinct user_id) dv
from temp_trade
group by dates -- 案例中要求计算每日pv、uv、dv，因此按日期分组
```

#### DV

visit depth，浏览深度（访问深度）。dv = pv / uv。用户在一次浏览网站的过程中浏览了网站的页数。如果用户一次性的浏览了你的网站的页数越多，那么就基本上可以认定，你的网站有他感兴趣的东西。用户访问网站的深度用数据可以理解为网站平均访问的页面数，就是pv和uv的比值，这个比值越大，用户体验度越好，网站的粘性也越高。

#### 留存、留存率

留存：相对于基准日活跃用户数量，n天后返回的用户数量。

**⚠️注意：留存计算的是n天后返回的用户数量，而不是n天后的用户数量。**

**比如，基准日有10个用户，次日也有10个，但其中8个用户是基准日的，剩下两个2个是新用户。次日留存是8而不是10。**

留存率：n天后返回的用户数/基准日活跃用户数*100%

留存率是用于反映网站、互联网应用或网络游戏的运营情况的统计指标，其具体含义为在统计周期（周/月）内，每日活跃用户数在第N日仍启动该App的用户数占比的平均值。其中N通常取2、3、7、14、30，分别对应次日留存率、三日留存率、周留存率、半月留存率和月留存率。留存率常用于反映用户粘性，当N取值越大、留存率越高时，用户粘性越高。

先计算各日留存，进而就可以求出各日留存率。

**❌错误思路：统计出每日活跃用户数，再按照日期筛选出对应日期的活跃用户数。**

```sql
-- 先对源数据按日期分组，然后count每日的user_id并去重，得到每日的活跃用户数。
create view wrong1 as
select dates, count(distinct user_id) device_v
from temp_trade
group by dates
```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_18-47-58.jpg)

```sql
-- 然后根据需求，筛选出对应日期的活跃用户数即可。
select 
	dates _dates, device_v,
	(select device_v from wrong1 where dates = date_add(_dates,interval 1 day)) day_1,
	(select device_v from wrong1 where dates = date_add(_dates,interval 2 day)) day_2,
	(select device_v from wrong1 where dates = date_add(_dates,interval 3 day)) day_3,
	(select device_v from wrong1 where dates = date_add(_dates,interval 7 day)) day_7
from wrong1
```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_19-12-56.jpg)

**错误原因：**

> 第一步统计每日活跃用户数看似正确，但这并不能计算出留存。
>
> 比如，计算出1号有100个用户，2号有100个用户，那么1号的次日留存就是2号的100吗？并不是。
>
> 留存计算的是相对于基准日，n天后返回的用户数。重点在“返回”二字。基准日1号有100个用户，次日2号100个用户中，可能有80个是1号100个用户中的，剩下20个是2号新增的用户。所以1号的次日留存只有80，而不是100。
>
> 所以计算留存，并不是简单的统计出每日的活跃用户数，然后按照日期筛选。
>
> 而是要统计1号的user_id，在这批user_id中看2号出现了多少个、3号出现了多少个。再统计2号的user_id、3号的user_id...以此类推。

**✅正确思路：统计每个日期有多少user_id，再统计每个日期的所有user_id在之后的2日、3日、...n日中出现的个数。**

1. ```sql
   select user_id, dates from temp_trade group by user_id,dates
   -- 按user_id,dates分组，得到全部日期-每个日期的全部活跃用户(无重复) 结果集
   ```

   ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_22-42-45.jpg) ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_22-43-07.jpg)

2. ```sql
   select *
   from 
   (select user_id, dates from temp_trade group by user_id,dates) a 
   left join 
   (select user_id, dates from temp_trade group by user_id,dates) b 
   on a.user_id = b.user_id
   -- 1结果集on user_id自关联，得到全部活跃用户-每个用户全部活跃日期-某个用户某一个活跃日期-某个用户全部活跃日期
   ```

   ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_22-52-09.jpg)

3. ```sql
   select *
   from 
   (select user_id, dates from temp_trade group by user_id,dates) a 
   left join 
   (select user_id, dates from temp_trade group by user_id,dates) b 
   on a.user_id = b.user_id
   where b.dates >= a.dates
   -- where过滤，得到全部活跃用户-每个用户全部活跃日期-某个用户某一个活跃日期-某个用户在该日期之后的全部活跃日期
   ```

   ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_23-02-23.jpg)

4. ```sql
   select 
   	a.dates,
   	count(distinct a.user_id) device_v
   from 
   (select user_id, dates from temp_trade group by user_id,dates) a
   left join 
   (select user_id, dates from temp_trade group by user_id,dates) b 
   on a.user_id = b.user_id
   where b.dates >= a.dates
   group by a.dates
   -- 按日期分组，每个分组就是-每个日期全部活跃用户-每个用户大于该日期的全部活跃日期
   -- count(distinct a.user_id) 统计每个日期的活跃用户数，即基准日活跃用户数
   ```

   ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_23-06-28.jpg)

5. ```sql
   select 
   	a.dates,
   	count(distinct a.user_id) device_v,
   	count(if(datediff(b.dates,a.dates)=1,a.user_id,null)) remain1
   from 
   (select user_id, dates from temp_trade group by user_id,dates) a
   left join 
   (select user_id, dates from temp_trade group by user_id,dates) b 
   on a.user_id = b.user_id
   where b.dates >= a.dates
   group by a.dates
   -- 每个分组-每个日期全部活跃用户-每个用户大于该日期的全部活跃日期-每个用户大于该日期1天的记录-每个日期1天后还有多少用户
   ```

6. 以此类推

   ```sql
   create view user_remain_view as
   select 
   	a.dates,
   	count(distinct a.user_id) device_v,
   	count(if(datediff(b.dates,a.dates)=1,a.user_id,null)) remain1,
   	count(if(datediff(b.dates,a.dates)=2,a.user_id,null)) remain2,
   	count(if(datediff(b.dates,a.dates)=3,a.user_id,null)) remain3,
   	count(if(datediff(b.dates,a.dates)=4,a.user_id,null)) remain4,
   	count(if(datediff(b.dates,a.dates)=5,a.user_id,null)) remain5,
   	count(if(datediff(b.dates,a.dates)=6,a.user_id,null)) remain6,
   	count(if(datediff(b.dates,a.dates)=7,a.user_id,null)) remain7,
   	count(if(datediff(b.dates,a.dates)=15,a.user_id,null)) remain15,
   	count(if(datediff(b.dates,a.dates)=30,a.user_id,null)) remain30
   from correct1 a left join correct1 b 
   on a.user_id = b.user_id
   where b.dates >= a.dates
   group by a.dates
   ```

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-13_14-28-07.jpg)

留存率

```sql
select 
	dates, 
	device_v,
	concat(cast((remain1/device_v*100) as decimal(10,2)),'%') day_1,
	concat(cast((remain2/device_v*100) as decimal(10,2)),'%') day_2,
	concat(cast((remain3/device_v*100) as decimal(10,2)),'%') day_3,
	concat(cast((remain4/device_v*100) as decimal(10,2)),'%') day_4,
	concat(cast((remain5/device_v*100) as decimal(10,2)),'%') day_5,
	concat(cast((remain6/device_v*100) as decimal(10,2)),'%') day_6,
	concat(cast((remain7/device_v*100) as decimal(10,2)),'%') day_7,
	concat(cast((remain15/device_v*100) as decimal(10,2)),'%') day_15,
	concat(cast((remain30/device_v*100) as decimal(10,2)),'%') day_30
from user_remain_view
```

cast()函数将（任何类型的）值转换为指定的数据类型。

```sql
-- cast(值 as 数据类型)
-- 将值转换为DATE数据类型：
SELECT CAST("2017-08-29" AS DATE); 
```

decimal数据类型，decimal是比dobule类型精度更高的浮点类型

```sql
decimal(p, d)
-- p表示有效位数
-- d是表示小数点后的位数
```

#### RFM

- 统计出所有用户的r值、f值、m值，各个值取平均值，以高于或低于平均值为标准评价用户质量。

- RFM八大用户分类：

  ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-13_15-57-06.jpg)

**R（recency) 最近一次购买**

- R ，统计每个用户的r值——统计每个用户最近的发生购买的日期——统计每个用户最近的购买日期和标准日期的天数差——按天数差给每个用户打分，得分就是每个用户r值。

```sql
-- 按user_id分组，找出每个用户最近一次发生购买的日期
drop view if exists user_recency;
create view user_recency as
select user_id, max(dates) recency_date 
from temp_trade 
where behavior_type = 2 
group by user_id 
order by recency_date desc

-- 假设2019-12-18是统计日期，计算每个用户的最近购买日期和统计日期差多少天，按天数打分，打分就是r值
create view r_level as
select 
	user_id, 
	recency_date, 
	datediff('2019-12-18',recency_date) recen_num,
	(case
		when datediff('2019-12-18',recency_date) <=2 then 5
		when datediff('2019-12-18',recency_date) <=4 then 4
		when datediff('2019-12-18',recency_date) <=6 then 3
		when datediff('2019-12-18',recency_date) <=8 then 2
		else 1 end
	) r_value
from user_recency
```

**F（frequency）购买频率**

- F ，统计一定时间范围内（本案例中为整张表时间范围）每个用户发生购买的记录数，对记录数打分，分值就是F值 

```sql
drop view if exists user_buy_fre_view;
create view user_buy_fre_view as
select user_id,count(user_id) buy_frequency
from temp_trade 
where behavior_type = 2
group by user_id

create view f_level as
select user_id, buy_frequency,
	(case
		when buy_frequency <=2 then 1
		when buy_frequency <=4 then 2
		when buy_frequency <=6 then 3
		when buy_frequency <=8 then 4
		else 5 end
	)f_value
from user_buy_fre_view
```

**M（monetary）购买金额**

本案例数据中没有消费金额数据，所以本案例中不统计M指标。

用R、F值建立4大用户分类。（因为只有2个指标，每个指标有2种结果，组合就是2^2次方，4个结果。如果是3个指标，就是2^3次方，8个结果）

**整合结果**

本次数据中通过最近消费(R)和消费频率(F)建立RFM模型

- 重要高价值客户：指最近一次消费较近而且消费频率较高的客户
- 重要唤回客户：指最近一次消费较远且消费频率较高的客户
- 重要深耕客户：指最近一次消费较近且消费频率较低的客户 
- 重要挽留客户：指最近一次消费较远且消费频率较低的客户

我们按照最近一次消费的均值和消费频率的均值定高低界限。

```sql
-- 求R、F均值
select avg(r_value) r_avg from r_level -- 2.7939
select avg(f_value) f_avg from f_level -- 2.2606

select r_level.user_id, r_value, f_value,
( case 
	when r_value >2.7939 and f_value > 2.2606 then '重要高价值客户'
	when r_value <2.7939 and f_value > 2.2606 then '重要换回客户'
	when r_value >2.7939 and f_value < 2.2606 then '重要深耕客户'
	when r_value <2.7939 and f_value < 2.2606 then '重要挽留客户'
	end
	) user_class
from r_level, f_level
where r_level.user_id = f_level.user_id
```

### 商品指标

本案例数据中没有商品金额，因此只能从现有的数据中寻找分析指标：

商品id，用户行为类型(1-曝光、2-购买、3-加入购物⻋、4-加入收藏夹)，品类id

两个维度：商品维度和品类维度

- 商品维度

  商品点击量（曝光量），收藏量，加购量，购买次数，购买转化（该商品的所有用户中有购买转化的用户比）

  ```sql
  -- 商品
  select 
  	item_id, 
  	count(if(behavior_type=1,item_id,null)),
  	sum(if(behavior_type=1,1,0)) pv,
  	sum(if(behavior_type=4,1,0)) fav,
  	sum(if(behavior_type=3,1,0)) cart,
  	sum(if(behavior_type=2,1,0)) buy,
  	count(distinct if(behavior_type=2,user_id,null))/count(distinct user_id) buy_rate
  	from temp_trade group by item_id
  ```

- 品类维度

  品类点击量（曝光量），收藏量，加购量，购买次数，购买转化（该商品品类的所有用户中有购买转化的用户比)

  ```sql
  -- 品类 
  select 
  	item_category, 
  	count(if(behavior_type=1,item_category,null)),
  	sum(if(behavior_type=1,1,0)) pv,
  	sum(if(behavior_type=4,1,0)) fav,
  	sum(if(behavior_type=3,1,0)) cart,
  	sum(if(behavior_type=2,1,0)) buy,
  	count(distinct if(behavior_type=2,user_id,null))/count(distinct user_id) buy_rate
  	from temp_trade group by item_category
  ```

### 平台指标

两个维度

- 平台维度：平台每日的点击次数、收藏次数、加购次数、购买次数、购买转化

  ```sql
  	select 
  		dates, 
  		sum(if(behavior_type=1,1,0)) pv,
  		sum(if(behavior_type=4,1,0)) fav,
  		sum(if(behavior_type=3,1,0)) cart,
  		sum(if(behavior_type=2,1,0)) buy,
  		count(distinct if(behavior_type=2,user_id,null))/count(distinct user_id) buy_rate
  	from temp_trade 
  	group by dates
  ```

- 用户路径维度：

  用户行为路径是针对某一个用户购买某一件商品。

  ```sql
  drop view if exists path_base_view;
  create view path_base_view as
  select a.*
  from(
  select 
  	user_id,item_id,
  	lag(behavior_type,4) over(partition by user_id,item_id order by date_time) lag4,
  	lag(behavior_type,3) over(partition by user_id,item_id order by date_time) lag3,
  	lag(behavior_type,2) over(partition by user_id,item_id order by date_time) lag2,
  	lag(behavior_type,1) over(partition by user_id,item_id order by date_time) lag1,
  	behavior_type,
  	rank() over(partition by user_id,item_id order by date_time desc) rank_number
  from temp_trade
  )a
  where a.behavior_type = 2 and a.rank_number = 1 -- 只统计每个用户商品分组中，最后一次购买的记录
  
  select 
  	concat(ifnull(lag4,'空'),'-',ifnull(lag3,'空'),'-',ifnull(lag2,'空'),'-',ifnull(lag1,'空'),'-',behavior_type),
  	count(distinct user_id)
  from path_base_view
  group by concat(ifnull(lag4,'空'),'-',ifnull(lag3,'空'),'-',ifnull(lag2,'空'),'-',ifnull(lag1,'空'),'-',behavior_type)
  ```

  ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-14_15-25-13.jpg)

  为什么要分析用户行为路径？

  根据分析结果可以看到，空-空-空-空-2 这个路径的用户是最多的，也就是直接购买涉及的用户最多。这也反映出加入购物车、收藏等功能大家用的少。

  那在进行产品体验优化的时候就应该考虑，应该把购物车、收藏等功能丰富一下，看看怎么优化一下，让大家把它给用起来。

  为什么要让大家把购物车、收藏功能用起来呢。试想一下，你可能直接购买了一个商品，然后就去支付。是你也支付了，也在平台消费了，但是你毕竟买的是一个商品。如果有购物车功能，并且大家把它都用起来的话，就如同你去超市推了一个购物车，我们不由自主的就想往购物车里放更多的商品，那当你最终购买的时候，相比于你花了好几分钟时间只够买了一个商品，给平台带来的利益更大。

# 案例二 招聘网站岗位数据分析

## 项目背景

某线下培训机构 打算开设数据分析培训课程。需要对数据分析岗位的市场需求、就业情况、岗位技能做深入调研。

公司内已开设学科有：游戏、运维。

数据分析岗位的各项指标与已有学科进行对比。

本次数据范围，仅针对北上广深四个一线城市进行分析。

## 确认问题与目标拆解

![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-14_15-54-59.jpg)

# 作业

对案例一电商平台用户留存指标进行分析。

## 背景

基准日有记录的用户，在n日后仍有记录就算作留存用户。反之n日后没有记录就是流失用户。

## 提出问题

各日留存是否有异常波动？波动原因是什么？

用户流失的原因是什么？

## 分析思路

分析次日、3日、5日、7日流失用户的用户行为路径。

