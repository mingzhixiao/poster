#### 时间sql

#####  1.将时间转换成天数，然后计算天数

 统计以往的三十天的数据   to_days 可以将时间（date类型）转换成天

```sql
SELECT
id, store_id, store_name, shop_admin_id, captain_id, captain_name, trade_money, refund_money, 
trade_num, commission_money, stat_date, create_time, update_time
from
    t_captain_daily_trade_stat
where
    1 <= TO_DAYS(now()) -TO_DAYS(stat_date)
	&&	TO_DAYS(now()) -TO_DAYS(stat_date) <= 30
```
