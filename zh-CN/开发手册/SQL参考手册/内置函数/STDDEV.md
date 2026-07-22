```ebnf
stddev = STDDEV "(" [DISTINCT|UNIQUE|ALL] expr ")" [keep_clause].

keep_clause = KEEP "(" DENSE_RANK (FIRST|LAST) order_by_clause ")".
```

STDDEV函数计算[expr](../通用SQL语法/expr)的值的样本标准差。

当给定参数只有一行数据时，STDDEV函数的计算结果为0。

STDDEV函数的返回值类型有以下几种情况：

*   当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER类型时，返回NUMBER类型。
*   当expr的值为FLOAT、DOUBLE类型时，返回与参数一致的FLOAT、DOUBLE类型。
*   当expr的值为NULL时，返回NULL。
*   当expr的值为字面量、变量、常量时，返回0。
*   当expr的值为其他类型且无法转换为NUMBER类型时，不执行计算并返回类型转换错误。

其中expr的值为数值型或可以转换为NUMBER类型的字符型。对于其他类型，函数返回类型不支持。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。

**DISTINCT|UNIQUE**

表示过滤掉输入的重复数据后，进行样本标准差计算。

DISTINCT与UNIQUE完全等价。

**ALL**

默认值，表示不对表达式输入的重复数据进行过滤，直接进行样本标准差计算。

示例

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME           DHQ             EMPLOYEE_COUNT 
------- ------------------- --------------- -------------- 
01      EastChina              Shanghai                         
02      WestChina              Chengdu                    300   
03      SouthChina              Guangzhou                  400   
04      NorthChina              Beijing                    300   
05      CentralChina              Wuhan                       
 
--计算员工数量的样本标准差,为空的行将被忽略,该语句等同于SELECT STDDEV(ALL employee_count) FROM area
SELECT STDDEV(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269    
 
--只有一行记录时，样本标准差为0
SELECT STDDEV(employee_count) res FROM area1 where area_no='02';
                 RES
--------------------
                   0

--除去重复的员工数量后计算样本标准差
SELECT STDDEV(DISTINCT employee_count) res FROM area1;
                 RES
--------------------
          70.7106781
```

**keep_clause**

KEEP聚集函数通用语法，详细请查阅[KEEP聚集函数](00内置函数.md#KeepAggregateFunction)。

使用KEEP子句时STDDEV函数无法使用DISTINCT。

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

--统计最早的年数的薪资的样本标准差
SELECT STDDEV(revenue_total) KEEP (DENSE_RANK FIRST ORDER BY YEAR) FROM finance_info;

STDDEV(REVENUE_TOTAL)KEEP(DENSE_RANKFIRSTORDERBYYEAR)
-----------------------------------------------------
                                                    0

--统计最晚的年数的薪资的样本标准差
SELECT STDDEV(revenue_total) KEEP (DENSE_RANK LAST ORDER BY YEAR) FROM finance_info;

STDDEV(REVENUE_TOTAL)KEEP(DENSE_RANKLASTORDERBYYEAR)
----------------------------------------------------
                                           4605.1659
```
