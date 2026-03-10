```ebnf+diagram
pow::= POW "(" expr "," exp ")"
power::= POWER "(" expr "," exp ")"
```

POW/POWER函数计算[expr](../通用SQL语法/expr)参数值的exp次幂，返回一个DOUBLE类型的数值。

**exp**

指数。

exp为与expr相同的通用表达式，当exp的值或expr的值为NULL时，函数返回NULL。

不允许对BLOB和TEXT类型数据使用此函数。

示例（单机HEAP表）

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
