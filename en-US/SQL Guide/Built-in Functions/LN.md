```ebnf+diagram
ln::= LN "(" expr ")"
```

The LN function is used to compute the natural logarithm of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), returning a value of type DOUBLE.

The value of expr must be numeric or a string that can be converted to NUMBER type (conversion failure returns Invalid number error). For other types, the function will return type not supported.

When the value of expr is NULL, the function returns NULL.

Based on the mathematical concept of natural logarithm, the value of expr should be a positive number. The table below lists the return rules of the function for non-positive numbers and some special values:

| expr | LN(expr) |
| ---- | -------- |
| Negative number | Nan      |
| 0    | -Inf     |
| Inf  | Inf      |
| -Inf | Nan      |
| Nan  | Nan      |
| -Nan | Nan      |

***Example***

```sql
SELECT LN(2) res FROM DUAL;
RES
-----------
6.931E-001

CREATE TABLE number_fd(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd VALUES(0,-5.55);
INSERT INTO number_fd VALUES('Inf','-Inf');
INSERT INTO number_fd VALUES('Nan','-Nan');

SELECT LN(numberf) res1,LN(numberd) res2 FROM number_fd;
       RES1        RES2 
----------- ----------- 
       -Inf         Nan
        Inf         Nan
        Nan         Nan
```
