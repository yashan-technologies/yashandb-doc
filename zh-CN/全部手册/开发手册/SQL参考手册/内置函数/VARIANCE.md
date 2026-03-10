```ebnf+diagram
variance::= VARIANCE "(" [DISTINCT|ALL] expr ")" [keep_clause]

keep_clause::= KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")"
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

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](00内置函数.html#KeepAggregateFunction)。

使用KEEP子句时VARIANCE函数无法使用DISTINCT。

示例

```sql
--finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

--统计最早的年数的薪资的样本方差
SELECT VARIANCE(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

VARIANCE(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
-------------------------------------------------------
                                                      0

--统计最晚的年数的薪资的样本方差
SELECT VARIANCE(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

VARIANCE(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
------------------------------------------------------
                                            21207552.9
```
