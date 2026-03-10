```ebnf+diagram
variance::= VARIANCE "(" [DISTINCT|ALL] expr ")"
```

VARIANCE函数计算[expr](../通用SQL语法/expr)的值的样本方差。

当给定参数只有一行数据时，VARIANCE函数的计算结果为0。  

VARIANCE函数的返回值类型有以下几种情况：

*   当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER类型时，返回NUMBER类型。
*   当expr的值为FLOAT、DOUBLE类型时，返回与expr的值一致的FLOAT、DOUBLE类型。
*   当expr的值为NULL时，返回NULL。
*   当expr的值为字面量、变量、常量时，返回0。
*   当expr的值为其他类型且无法转换为NUMBER类型时，不执行计算并返回类型转换错误。

**DISTINCT**

表示过滤掉输入的重复数据后，进行样本方差计算。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行样本方差计算。


示例

```sql
--计算员工数量的样本方差,为空的行将被忽略,该语句等同于SELECT VARIANCE(ALL employee_count) FROM area1
SELECT VARIANCE(employee_count) res FROM area1;
                 RES
--------------------
          3333.33333
 
--只有一行记录的样本方差为0
SELECT VARIANCE(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                   0

--除去重复的员工数量后计算样本方差
SELECT VARIANCE(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
                5000
```

