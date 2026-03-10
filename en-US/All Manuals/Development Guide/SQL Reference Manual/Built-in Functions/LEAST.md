```ebnf+diagram
least::= LEAST "(" (expr) {"," (expr)} ")"
```

LEAST finds the smallest value from multiple [expr](../General SQL Syntax/expr) parameter values and returns the result according to the data type of the first parameter. The rules are as follows:

*   When all expr values are numeric, comparison is done based on size.
*   When the first expr value is numeric and some values are character types, the character types are converted to NUMBER type for comparison; if conversion fails, an Invalid number error is returned.
*   When all expr values are character types, comparison is done lexicographically.
*   When the first expr value is a character type and some values are non-character types, the non-character types are converted to character types, and comparison is done lexicographically.
*   When all expr values are date-time types, comparison is done based on time size.
*   When all expr values are boolean types, comparison is done with true=1 and false=0.
*   If any expr value in the parameter list is NULL, the function returns NULL.
*   If expr is LOB or JSON, the function returns an unexpected lob error.
*   In other cases, the function returns an Invalid datatype error.

  

***Example***

```sql
SELECT LEAST(2, 5, 12, 3, 16, 8, 9) AS RES1,
LEAST('A', 6, 7, 5000, 'E', 'F','G') AS RES2,
LEAST(SYSDATE,TO_DATE('2014-08-01','YYYY-MM-DD')) AS RES3,
LEAST(true,false) RES4
FROM DUAL;
        RES1 RES2  RES3                             RES4                
------------ ----- -------------------------------- --------------------
           2 5000  2014-08-01 00:00:00              false
```
