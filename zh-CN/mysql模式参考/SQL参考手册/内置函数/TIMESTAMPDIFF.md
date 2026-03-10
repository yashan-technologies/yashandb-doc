```ebnf+diagram
timestampdiff::= TIMESTAMPDIFF "(" unit "," expr1 "," expr2 ")" 
```

TIMESTAMPDIFF函数根据unit所指定的时间单位，计算expr1与expr2之间的时间差，返回一个BIGINT类型的数值，expr1小于expr2时返回值为正，expr1大于expr2时返回值为负。

**unit**

表示函数计算结果的单位，unit不可以为NULL，且必须为如下字符字面量中的一项：

- MICROSECOND
- SECOND
- MINUTE
- HOUR
- DAY
- WEEK
- MONTH
- QUARTER
- YEAR

**expr1/expr2**

expr1和expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr)，其值须为TIMESTAMP、DATE、TIME类型或可以转换为TIMESTAMP、DATE类型的字符型。

当expr1或者expr2的值为NULL时，函数返回NULL。

当expr1或者expr2的值为DATE类型时，系统对微秒部分补0。

当expr1或者expr2的值为TIME类型时，系统对缺少的日期部分补充为当天日期值。

示例（单机HEAP表）

```sql
SELECT TIMESTAMPDIFF(DAY,'1583-10-01 08:00:00','2000-09-30 07:59:59') res FROM DUAL;
                  res 
--------------------- 
               152305

SELECT TIMESTAMPDIFF(DAY,'2022-3-1','2022-4-1') res FROM DUAL;
                  res
---------------------
                   31

SELECT TIMESTAMPDIFF(DAY,'2022-4-1','2022-3-1') res FROM DUAL;
                  res
---------------------
                  -31
```
