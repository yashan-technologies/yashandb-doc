```ebnf+diagram
from_tz::= FROM_TZ "(" timestamp_value "," time_zone_value ")"
```

The FROM_TZ function has 2 [expr](../General SQL Syntax/expr) parameters. It converts the date value corresponding to the parameter timestamp_value and the time zone corresponding to the parameter time_zone_value into a TIMESTAMP WITH TIME ZONE type value.

**timestamp_value**

The data type of the expression timestamp_value must be TIMESTAMP.

When the value of timestamp_value is NULL, the function will check its data type. If it is TIMESTAMP, the function returns NULL; otherwise, it will raise an error indicating that the type is not supported.

**time_zone_value**

The data type of the expression time_zone_value must be character type, formatted as 'TZH:TZM'.

When the value of time_zone_value is NULL, the function returns NULL.

***Example***

```sql
SELECT FROM_TZ(TIMESTAMP '2025-05-21 08:00:00', '3:00') FROM DUAL;

FROM_TZ(TIMESTAMP'2025-05-2108:00:00','3:00')
----------------------------------------------------------------
2025-05-21 08:00:00.000000 +03:00
```
