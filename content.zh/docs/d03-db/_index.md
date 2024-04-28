---
title: "数据库"
weight: 3
---

## MySQL

## ClickHouse

查询示例：将时间按 interval设置取整， 统计某段时间内 不同 return_code 的数量

```sql
select toStartOfInterval(create_data_time, interval 1 day) as create_data_time, return_code,
sum(call_num0)
from "euop_dwm"."t_dwm_euop_channel_qps_day"
where $__timeFilter(create_data_time)
group by create_data_time, return_code
order by return_code
limit 100
```

根据external_return_code 进行统计 指定时间内 不同 external_return_code的数量

```sql
select external_return_code, 
count(external_return_code) as count_return_code
from "euop_dwd"."t_dwd_euop_external_log"
where (iop_code = '2371') and ( create_data_time >= $__fromTime and create_data_time <= $__toTime)
group by external_return_code
limit 100
```

## Redis

## Sqlite
