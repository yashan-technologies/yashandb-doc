```ebnf+diagram
from_unixtime::= FROM_UNIXTIME "(" unix_time ["," format_expr ] ")" 
```

FROM_UNIXTIME函数输入从'1970-01-01 00:00:00' UTC 开始的秒数与时间日期格式，计算并返回DATETIME类型或VARCHAR类型。

**unix_time**

表示从 '1970-01-01 00:00:00' UTC 开始的秒数，BIGINT类型或可转换为BIGINT类型的其他类型。

**format_expr**

可选参数，表示时间日期格式，VARCHAR类型或可转换为VARCHAR类型的其他类型。

若只输入unix_time参数，函数返回DATETIME类型；若输入了format参数，函数返回VARCHAR类型。

示例（单机HEAP表）

```sql
SELECT FROM_UNIXTIME(1447430881);

from_unixtime(1447430881)                                        
---------------------------------------------------------------- 
2015-11-14 00:08:01.000000

SELECT FROM_UNIXTIME(1447430881, '%Y %D %M %h:%i:%s %x');

from_unixtime(1447430881, '%Y %D %M %h:%i:%s %x')                
---------------------------------------------------------------- 
2015 14th November 12:08:01 2015

```
