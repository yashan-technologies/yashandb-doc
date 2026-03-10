```ebnf+diagram
DAYOFWEEK::= DAYOFWEEK "(" expr ")" 
```

DAYOFWEEK函数用于计算[expr](../../全部手册/开发手册/SQL参考手册/通用SQL语法/expr)位于所在周的第几天（以周日为第一天计算），返回一个INT类型的数值。

本函数不支持向量化计算。

expr的值须为TIMESTAMP/DATE类型或可以转换为TIMESTAMP/DATE类型的字符型。

当expr的值为NULL时，函数返回NULL。

> **Note**: 
>
> YashanDB采用外推格历高利历，1582年10月15号（不包括15号）之前与Oracle历法不一致。 

示例（HEAP表）

```sql
SELECT DAYOFWEEK(DATE '2022-7-26') res FROM DUAL;
         RES 
------------ 
           3

--微秒数超过6位时按四舍五入进位
SELECT DAYOFWEEK('2022-10-28 23:59:59.999999999') res FROM DUAL;
         RES 
------------ 
           7
           
SELECT DAYOFWEEK('2022-10-28 23:59:59.99999901') res FROM DUAL;
         RES 
------------ 
           6
```
