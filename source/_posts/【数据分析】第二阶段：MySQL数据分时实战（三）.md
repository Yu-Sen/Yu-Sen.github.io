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
   -- 按user_id,dates分组，得到全部日期-每个日期的全部活跃用户 结果集
   ```

   ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_22-42-45.jpg) ![](https://gitee.com/ethan-H/imghost/raw/master/blog/Xnip2021-05-12_22-43-07.jpg)

2. ```sql
   select *
   from 
   (select user_id, dates from temp_trade group by user_id,dates) a 
   left join 
   (select user_id, dates from temp_trade group by user_id,dates) b 
   on a.user_id = b.user_id
   -- 1结果集自关联，得到全部活跃用户-每个用户全部活跃日期-某个用户某一个活跃日期-某个用户全部活跃日期
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





