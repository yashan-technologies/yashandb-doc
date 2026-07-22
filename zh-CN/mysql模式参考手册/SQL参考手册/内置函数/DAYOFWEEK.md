```ebnf
dayofweek = DAYOFWEEK "(" expr ")" .
```

DAYOFWEEK函数用于计算[expr](../通用SQL语法/expr)位于所在周的第几天（以周日为第一天计算），返回一个INT类型的数值。

**expr**

expr的值须为TIMESTAMP/DATE类型或可以转换为TIMESTAMP/DATE类型的其他类型。

* 根据[SQL_MODE](../../系统变量/系统变量参考.md#sql_mode)中是否包含NO_ZERO_DATE和NO_ZERO_IN_DATE决定expr的值是否允许年月日中出现0。
* 当expr的值为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT DAYOFWEEK(DATE '2022-7-26') res FROM DUAL;
         res
------------
           3

--微秒数超过6位时按四舍五入进位
SELECT DAYOFWEEK('2022-10-28 23:59:59.999999999') res FROM DUAL;
         res
------------
           7

SELECT DAYOFWEEK('2022-10-28 23:59:59.99999901') res FROM DUAL;
         res
------------
           6
```
