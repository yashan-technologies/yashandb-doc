```ebnf+diagram
from_unixtime::= FROM_UNIXTIME "(" unix_time ["," format_expr ] ")" 
```

The FROM_UNIXTIME function takes the number of seconds since '1970-01-01 00:00:00' UTC and a date-time format, calculates, and returns a DATETIME type or VARCHAR type.

**unix_time**

Represents the number of seconds since '1970-01-01 00:00:00' UTC, of BIGINT type or other types that can be converted to BIGINT.

**format_expr**

An optional parameter that represents the date-time format, of VARCHAR type or other types that can be converted to VARCHAR.

If only the unix_time parameter is provided, the function returns DATETIME type; if the format parameter is provided, the function returns VARCHAR type.

***Example*** for Standalone Deployment Heap tables

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
