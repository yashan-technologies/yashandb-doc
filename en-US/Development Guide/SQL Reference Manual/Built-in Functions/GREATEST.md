```ebnf
greatest = GREATEST "(" expr {"," expr} ")".
```

The GREATEST function finds the maximum value among multiple parameters [expr](../General SQL Syntax/expr) and returns the result in the data type of the first parameter. The rules are as follows:

* When all expr values are numeric, comparison is made based on size.
* When the first expr value is numeric and some values are character type, the character types are converted to NUMBER type for comparison; if the conversion fails, an Invalid number error is returned.
* When all expr values are character types, comparison is made based on lexicographic order.
* When the first expr value is a character type and some values are non-character types, non-character types are converted to character types, then comparison is made based on lexicographic order.
* When all expr values are date-time types, comparison is made based on time.
* When all expr values are boolean types, comparison is made using true=1 and false=0.
* If any expr value in the argument list is NULL, the function returns NULL.
* If expr is LOB or JSON, the function returns an unexpected lob error.
* For all other situations, the function returns an Invalid datatype error.

  

***Example***

```sql
SELECT GREATEST(2, 5, 12, 3, 16, 8, 9) AS RES1,
GREATEST('A', 6, 7, 5000, 'E', 'F','G') AS RES2,
GREATEST(sysdate,TO_DATE('2014-08-01','YYYY-MM-DD')) AS RES3,
GREATEST(true,false) RES4
FROM DUAL;
        RES1 RES2  RES3                             RES4                
------------ ----- -------------------------------- --------------------
          16 G     2021-12-02 23:09:54              true
```
