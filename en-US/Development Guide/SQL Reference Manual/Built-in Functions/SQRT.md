```ebnf
sqrt = SQRT "(" expr ")".
```

The SQRT function calculates the square root of the value represented by [expr](../General SQL Syntax/expr). Its return type is:

*   When the value of expr is of type TINYINT, SMALLINT, INT, BIGINT, NUMBER, CHAR, VARCHAR, NCHAR, or NVARCHAR, it returns NUMBER.
*   When the value of expr is of type FLOAT, it returns FLOAT.
*   When the value of expr is of type DOUBLE, it returns DOUBLE.
*   When the value of expr is NULL, it returns NULL.

The value of expr must be a numeric type or a character type that can be converted to NUMBER (conversion failure returns an Invalid number error). For other types, the function return type is not supported.

When the value of expr is a negative number, the function returns an Out of range error. If the negative number is of type float or double, the function returns an invalid number Nan.

***Example***

```sql
-- The table numbers_nobit contains the following fields and data
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT SQRT(numberb) sqrt1,
SQRT(numberc) sqrt2,
SQRT(numberd) sqrt3,
SQRT(numbere) sqrt4,
SQRT(numberg) sqrt5
FROM numbers_nobit;
      SQRT1       SQRT2       SQRT3       SQRT4       SQRT5
----------- ----------- ----------- ----------- -----------
 7.41619849  74.5318724  2357022604  2.357E+000   23.558438
 
 
SELECT TYPEOF(SQRT(numberb)) type1,
TYPEOF(SQRT(numberc)) type2,
TYPEOF(SQRT(numberd)) type3,
TYPEOF(SQRT(numbere)) type4,
TYPEOF(SQRT(numberg)) type5
FROM numbers_nobit;
TYPE1       TYPE2       TYPE3      TYPE4     TYPE5       
----------- ----------- ---------- --------- ---------   
number      number      number     float     number
```
