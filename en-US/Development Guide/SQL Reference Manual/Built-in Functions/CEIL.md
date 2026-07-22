```ebnf
ceil = CEIL "(" expr ")".
```

The CEIL function rounds the value represented by [expr](../General SQL Syntax/expr) upwards, with the following return rules:

* When the value of expr is numeric, it returns a value of the same data type.
* When the value of expr is character type, it returns a value of NUMBER type.
* When the value of expr is NULL, it returns NULL.
* When the value of expr is a special floating-point value:

    * Nan: the function returns Nan
    * Inf: the function returns Inf
    * \-Inf: the function returns -Inf

The value of expr must be numeric or a character type convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function returns type not supported.

***Example***

```sql
SELECT CEIL(7) ceil1,
CEIL('0.97') ceil2,
CEIL(6.22) ceil3
FROM DUAL;
       CEIL1       CEIL2       CEIL3
------------ ----------- -----------
           7           1           7
 
-- Create number_fd table, containing FLOAT and DOUBLE fields
CREATE TABLE number_fd1(numberf FLOAT, numberd DOUBLE);
INSERT INTO number_fd1 VALUES('Nan','Nan');
INSERT INTO number_fd1 VALUES('Inf','Inf');
INSERT INTO number_fd1 VALUES('-inf','-inf');
COMMIT;
 
SELECT CEIL(numberf) ceilf,
CEIL(numberd) ceild
FROM number_fd1;
      CEILF       CEILD
----------- -----------
        Nan         Nan
        Inf         Inf
       -Inf        -Inf
```
