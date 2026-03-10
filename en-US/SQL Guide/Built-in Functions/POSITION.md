```ebnf+diagram
position::= POSITION "(" expr1 IN expr2 ")"
```

The POSITION function searches for the string represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)1 from left to right within the string represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)2 and returns the position of the first occurrence of expr1 as a BIGINT type number. If no match is found, the function returns 0.

- The values of expr1 and expr2 must be of character type or other types that can be converted to character type, excluding JSON, LOB, and BFILE types.

- If either expr1 or expr2 is NULL, the function returns NULL.

***Example***

```sql
SELECT employee_name n,
POSITION('a' IN employee_name) p
FROM employees;
N                                 P
------------- ---------------------
Mask                              2
John                              0
Anna                              4
Jack                              2
Jim                               0
```
