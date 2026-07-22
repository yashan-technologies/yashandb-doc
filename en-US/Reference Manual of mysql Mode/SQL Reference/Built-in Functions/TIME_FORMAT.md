```ebnf
time_format = TIME_FORMAT "(" time "," format ")" .
```

The TIME_FORMAT function extracts TIME type data specified by the time parameter according to the format string specified by the format parameter, and returns the resulting string. The valid values for format are the same as the format parameter in the [DATE_FORMAT](DATE_FORMAT) function, but the TIME_FORMAT function returns NULL or 0 for formats other than hour, minute, second, and microsecond.

When the hour in the time parameter is greater than 23, the return values for the %H and %k formats can be greater than 23, while return values for other hour-related formats are the remainder of hour divided by 12.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT TIME_FORMAT('2025-8-1 23:12:56', '%H %k %h %I %l') res FROM DUAL;

res               
----------------- 
23 23 11 11 11
```
