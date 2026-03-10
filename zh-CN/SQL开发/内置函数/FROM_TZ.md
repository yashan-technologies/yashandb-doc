```ebnf+diagram
from_tz::= FROM_TZ "(" timestamp_value "," time_zone_value ")"
```

FROM_TZ函数有2个[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)参数。将参数timestamp_value对应的日期值和参数time_zone_value对应的时区转换为一个TIMESTAMP WITH TIME ZONE类型的值。

**timestamp_value**

表达式timestamp_value的数据类型须为TIMESTAMP类型。

当timestamp_value的值为NULL时，函数会判断其数据类型，为TIMESTAMP类型时函数返回NULL，否则会报错提示类型不支持。

**time_zone_value**

表达式time_zone_value的数据类型必须是字符型，格式为'TZH:TZM'。

当time_zone_value的值为NULL时，函数返回NULL。

示例

```sql
SELECT FROM_TZ(TIMESTAMP '2025-05-21 08:00:00', '3:00') FROM DUAL;

FROM_TZ(TIMESTAMP'2025-05-2108:00:00','3:00')
----------------------------------------------------------------
2025-05-21 08:00:00.000000 +03:00
```
