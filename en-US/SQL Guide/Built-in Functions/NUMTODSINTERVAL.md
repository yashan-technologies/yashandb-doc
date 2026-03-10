```ebnf+diagram
numtodsinterval::= NUMTODSINTERVAL "(" expr "," "'"(DAY|HOUR|MINUTE|SECOND)"'" ")"
```

The NUMTODSINTERVAL function converts the numeric data represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), excluding BIT, into an INTERVAL DAY TO SECOND value with units of DAY|HOUR|MINUTE|SECOND.

When the value of expr is NULL, it returns NULL.

When the value of expr is non-numeric data, it first converts it to NUMBER type. If the conversion fails, it returns an Invalid number error.

The case of DAY|HOUR|MINUTE|SECOND is not sensitive when specifying the units.

***Example***

```sql
SELECT NUMTODSINTERVAL('4','minute') res FROM DUAL;
RES            
--------------------------------
+00 00:04:00.000000            
 
-- Get the time value four minutes after the current time
SELECT SYSDATE, SYSDATE+NUMTODSINTERVAL('4','minute') sysdate2 FROM DUAL;
SYSDATE                          SYSDATE2                                  
-------------------------------- --------------------------------
2021-11-30 17:03:42              2021-11-30 17:07:42
```
