```ebnf
mod = MOD "(" expr1 "," expr2 ")".
```

MOD is the modulo function, consistent with the [arithmetic operator](../Operators/Arithmetic Operators) algorithm.

During arithmetic operations, YashanDB performs implicit data type conversion, unifying the data types involved in the operation to a specific data type, and returns the operation result accordingly. Please refer to the data type descriptions in the [arithmetic operators](../Operators/Arithmetic Operators) for specific rules.

The values of [expr](../General SQL Syntax/expr)1 and [expr](../General SQL Syntax/expr)2 must be numeric or character types convertible to NUMBER type (conversion failure returns Invalid number error). For other types, the function return type is not supported.

If either expr1 or expr2 has a value of NULL, the function returns NULL.

***Example***

```sql
SELECT numbera,numberb,numberc,numberd,numbere,numberf,numberg FROM numbers_nobit;
 NUMBERA NUMBERB  NUMBERC               NUMBERD     NUMBERE     NUMBERF   NUMBERG
-------- ------- -------- --------------------- ----------- ----------- ---------
      -5      55     5555   5555555555555555555  5.555E+000  5.556E+000       555
 
 
SELECT MOD(numberb,numbera) mod1,
MOD(numberd,numbere) mod2,
MOD(numberf,numberc) mod3,
MOD(numbera,'17') mod4,
MOD(numberf,0) mod5
FROM numbers_nobit;
                 MOD1        MOD2        MOD3        MOD4        MOD5
--------------------- ----------- ----------- ----------- -----------
                    0  3.794E+000  5.556E+000          -5  5.556E+000
 
SELECT TYPEOF(MOD(numberb,numbera)) type1,
TYPEOF(MOD(numberd,numbere)) type2,
TYPEOF(MOD(numberf,numberc)) type3,
TYPEOF(MOD(numbera,'17')) type4,
TYPEOF(MOD(numberf,0)) type5
FROM numbers_nobit;
TYPE1     TYPE2     TYPE3     TYPE4     TYPE5       
--------- --------- --------- --------- ---------   
bigint    float     double    number    double
```
