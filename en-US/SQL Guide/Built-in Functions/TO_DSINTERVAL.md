```ebnf+diagram
to_dsinterval::= TO_DSINTERVAL "(" "'" sql_format "'" ")" 

sql_format::= (["+" | "-"] days hours ":" minutes ":" seconds ["." frac_secs])
```

The TO_DSINTERVAL function converts the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to a value of INTERVAL DAY TO SECOND type.

When the value of expr is NULL, the function returns NULL.

The value of expr should be character data; otherwise, it returns a type unsupported error. The string must conform to the sql_format format requirements; otherwise, it returns a format conversion error.

**sql_format**

SQL interval format compliant with SQL standard (ISO/IEC 9075), needing to declare the five elements: days, hours, minutes, seconds, and frac_secs. Days and hours should be separated by one or more spaces, while hours, minutes, and seconds should be separated by a colon. Extra spaces are allowed between different elements, for example, `'23 23    : 23:12'`.

- days: Integer, range is [-100000000,100000000].

- hours: Integer, range is [0,23].

- minutes: Integer, range is [0,59].

- seconds: Integer, range is [0,59].

- frac_secs: Decimal, range is [.0,.999999].

***Example***

```sql
SELECT TO_DSINTERVAL('23 23 : 23:12') res FROM DUAL;
RES            
--------------------------------
+23 23:23:12.000000        
 
-- Query time value two days before the current date
SELECT SYSDATE+TO_DSINTERVAL('-2 00:00:00') res FROM DUAL;
RES            
--------------------------------
2021-11-27 22:32:25
```
