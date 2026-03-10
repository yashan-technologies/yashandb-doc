```ebnf+diagram
numtoyminterval::= NUMTOYMINTERVAL "(" expr "," "'"(YEAR|MONTH)"'" ")"
```

The NUMTOYMINTERVAL function converts numeric data represented by [expr](../General SQL Syntax/expr) to an INTERVAL YEAR TO MONTH type value, excluding BIT data type.

It returns NULL when the value of expr is NULL.

When expr's value is non-numeric, it will first attempt to convert to NUMBER type; if the conversion fails, it returns an Invalid number error.

When specifying YEAR|MONTH units, the case of YEAR|MONTH is not case-sensitive. The valid range for expr values is as follows:

- years: integer, value range is [-178000000,178000000].

- months: integer, value range is [0,11].

***Example***

```sql
SELECT NUMTOYMINTERVAL('4','year') res FROM DUAL;
RES
--------------------
+04-00
```
