```ebnf
abs = ABS "(" expr ")".
```

The ABS function calculates the absolute value of a number, and its return value data types are as follows:

|Parameter Type |Return Value |
| --- | --- |
| TINYINT | SMALLINT |
| SMALLINT | INT |
| INT | BIGINT |
| BIGINT | BIGINT/NUMBER |
| NUMBER | NUMBER |
| FLOAT | FLOAT |
| DOUBLE | NUMBER |
| CHAR | NUMBER |
| VARCHAR | NUMBER |
| NCHAR | NUMBER |
| NVARCHAR | NUMBER |

The value of [expr](../General SQL Syntax/expr) should be numeric or a character type convertible to NUMBER (conversion failure returns an Invalid number error). For other types, the function returns type not supported.

When the value of expr is NULL, the function returns NULL.

***Example***

```sql
SELECT ABS(-2.345) abs1, 
ABS(1/10) abs2, 
ABS(1-2*3) abs3, 
ABS(TO_NUMBER('$3.33','$9.99')) abs4 
FROM DUAL;
 
       ABS1        ABS2                  ABS3        ABS4
----------- ----------- --------------------- -----------
      2.345          .1                     5        3.33
```
