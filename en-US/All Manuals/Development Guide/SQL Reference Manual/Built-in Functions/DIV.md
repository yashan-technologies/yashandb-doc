```ebnf+diagram
div::= DIV "(" expr1 "," expr2 ")"
```

The DIV function performs division operations, and the rules are as follows:

*   For decimal values (FLOAT/DOUBLE/NUMBER), the operations are consistent with [Arithmetic Operators](../Operators/Arithmetic Operators)/algorithms.
*   For integers, it performs integer division and returns the quotient.

During arithmetic operations, YashanDB implicitly converts the data types of the participating operands to a unified data type and returns the result according to this data type. For specific rules, please refer to the data type descriptions in the [Arithmetic Operators](../Operators/Arithmetic Operators) section.

The values of [expr](../General SQL Syntax/expr)1 and [expr](../General SQL Syntax/expr)2 must be numeric types or character types that can be converted to NUMBER (conversion failures return an "Invalid number" error). For other types, the function returns "unsupported operation."

When either expr1 or expr2 is NULL, the function returns NULL.

When the value of expr2 is 0 and the value of expr1 is a non-floating-point number, the function returns a "Divided by zero" error.

When expr1 or expr2 is a special floating-point value or 0, the processing rules are as follows:

| expr1 | expr2 | DIV(expr1,expr2) |
| --- | --- | --- |
| Nan | any number | Nan |
| any number | Nan | Nan |
| any number except Nan/Inf/-Inf | Inf/-Inf | 0   |
| Inf/-Inf | any number except Nan/Inf/-Inf | Inf/-Inf |
| Inf/-Inf | Inf/-Inf | Nan |
| positive floating-point number | 0   | Inf |
| negative floating-point number | 0   | \-Inf |

***Example***

```sql
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
     NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF     NUMBERG
------------ ------- -------- --------------------- ----------- ----------- -----------
          -5      55     5555   5555555555555555555  5.555E+000  5.556E+000         555
 
SELECT DIV(numberb,numbera) div1,
DIV(numberd,numbere) div2,
DIV(numberf,numberc) div3,
DIV(numbera,'17') div4,
DIV(numberf,0) div5
FROM numbers_nobit;
                DIV1        DIV2        DIV3        DIV4        DIV5
--------------------- ----------- ----------- ----------- -----------
                  -11    1.0E+018    1.0E-003  -.29411765         Inf
 
SELECT TYPEOF(DIV(numberb,numbera)) type1,
TYPEOF(DIV(numberd,numbere)) type2,
TYPEOF(DIV(numberf,numberc)) type3,
TYPEOF(DIV(numbera,'17')) type4,
TYPEOF(DIV(numberf,0)) type5
FROM numbers_nobit;
TYPE1      TYPE2    TYPE3     TYPE4    TYPE5    
---------- -------- --------- -------- ---------
bigint     float    double    number   double   
 
SELECT DIV(CAST('Nan' AS FLOAT), CAST('Inf' AS DOUBLE)) ndi,
DIV(CAST('-Inf' AS FLOAT), 0) id0,
DIV(12, CAST('-Inf' AS DOUBLE)) ndi,
DIV(12, CAST('Nan' AS FLOAT)) mdn,
DIV(CAST('-Inf' AS FLOAT), CAST('Inf' AS DOUBLE)) idi
FROM DUAL;
        NDI         ID0         NDI         MDN         IDI
----------- ----------- ----------- ----------- -----------
        Nan        -Inf           0         Nan         Nan
```
