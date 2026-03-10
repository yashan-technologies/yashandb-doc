```ebnf+diagram
to_yminterval::= TO_YMINTERVAL "(" "'" ym_sql_format "'" ")" 

ym_sql_format::= (["+"|"-"] years "-" months)
```

The TO_YMINTERVAL function converts the value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) to a value of the INTERVAL YEAR TO MONTH type.

When the value of expr is NULL, the function returns NULL.

The value of expr should be of character type; otherwise, it returns unsupported type. The string must comply with the ym_sql_format format; otherwise, it returns a format conversion error.

**ym_sql_format**

A SQL interval format compatible with the SQL standard (ISO/IEC 9075) requires the declaration of two elements: years and months, separated by a hyphen, and allows extra spaces, for example, `'5-  2'`.

- years: integer, with a range of [-178000000, 178000000].

- months: integer, with a range of [0, 11].

***Example***

```sql
SELECT TO_YMINTERVAL('5-2') res FROM DUAL;
RES
--------------------
+05-02  
 
-- Query the time value five years before the current date
SELECT SYSDATE+TO_YMINTERVAL('-5-0') res FROM DUAL;
RES            
--------------------------------
2016-11-29 22:51:41
```
