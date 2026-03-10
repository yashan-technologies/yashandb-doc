```ebnf+diagram
pow::= POW "(" expr "," exp ")"
power::= POWER "(" expr "," exp ")"
```

The POW/POWER function calculates the *exp* power of the value of [expr](../General SQL Syntax/expr) and returns a value of type DOUBLE.

**exp**

Exponent.

*exp* is a general expression that is the same as expr. When the value of *exp* or the value of expr is NULL, the function returns NULL.

The BLOB and TEXT type are not allowed to calculate with this function.

***Example*** for Standalone Deployment Heap tables

```sql

SELECT POW(2,2) RES ;

        RES
-----------
   4.0E+000

SELECT POW(2,-2) RES ;
        RES
----------- 
   2.5E-001

SELECT POW(2,2.1) RES ;
        RES
----------- 
 4.287E+000

SELECT POWER(2.2,-12.1) RES ;
        RES
----------- 
 7.189E-005
           
SELECT POW(-1,1.1) RES;
        RES
----------- 
        Nan

SELECT POW(0, -1) RES;
YAS-00040 data value out of range
```
