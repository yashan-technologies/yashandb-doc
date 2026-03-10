```ebnf+diagram
last_value::= LAST_VALUE "(" expr [(RESPECT|IGNORE) NULLS] ")" OVER "(" analytic_clause ")"
analytic_clause::= "(" [query_partition_clause] [order_by_clause [windowing_clause]] ")"
```

LAST_VALUE是一个作用于HEAP表上的[窗口函数](00内置函数（yashan模式）.html#WindowFunction)，并支持滑动窗口。本函数依据给定的窗口条件计算出窗口数据集合，并返回该集合的最后一行记录所对应的[expr](../通用SQL语法/expr)的值。如果最后一个值为NULL且未指定IGNORE NULLS，则函数返回 NULL。

窗口函数不可嵌套，因此expr为除窗口函数之外的其他通用表达式，其数据类型可以是除大对象型、JSON及UDT以外的数据类型。

**(RESPECT|IGNORE) NULLS**

指定expr的空值是否被包括在函数的计算中。默认值为RESPECT NULLS，即如果集合中的最后一个值为NULL，函数将返回NULL。

如果指定IGNORE NULLS，则函数返回集合中的最后一个非空值，当集合中所有值都为空时，返回NULL。此设置对于数据密集化很有用。

示例（HEAP表）

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME            DHQ             EMPLOYEE_COUNT
------- -------------------- --------------- --------------
01      EastChina               Shanghai                      
02      WestChina               Chengdu                    300
03      SouthChina               Guangzhou                  400
04      NorthChina               Beijing                    300
05      CentralChina               Wuhan                           

-- 返回按AREA_NO排序的每个窗口中的最后一个EMPLOYEE_COUNT,windowing_clause省略时的默认窗口为UNBOUNDED PRECEDIN至CURRENT ROW
SELECT LAST_VALUE(employee_count) OVER(ORDER BY AREA_NO) res FROM area1;
                 RES 
-------------------- 
                    
                 300
                 400
                 300
               

-- 返回按AREA_NO排序的每个窗口中的最后一个非空EMPLOYEE_COUNT,windowing_clause省略时的默认窗口为UNBOUNDED PRECEDIN至CURRENT ROW
SELECT LAST_VALUE(employee_count IGNORE NULLS) OVER(ORDER BY AREA_NO) res FROM area1;
         RES 
------------ 
            
         300
         400
         300
         300   
```

**analytic_clause**

窗口函数的通用语法。

在windowing_clause语句中无论指定的是ROWS还是RANGE，LAST_VALUE都将对参数列进行排序，以保证在指定RANGE的情况下本函数返回值的稳定性（当order_by_clause具有相同排名，则函数返回相同排名下任意一行的值都是合理的，由此产生了返回值的不稳定性 ），但ROWS情况下仍会存在此不稳定性。 

进行排序的参数列为：query_partition_clause中的expr 、order_by_clause中的排序列和LAST_VALUE函数的参数expr。

示例（HEAP表）

```sql
-- finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- 按年分组，并在每组记录中排名该年收入最低的机构
SELECT year,revenue_total,
LAST_VALUE(branch) OVER (PARTITION BY year ORDER BY revenue_total DESC) fr
FROM finance_info;
YEAR  REVENUE_TOTAL FR    
----- ------------- ----- 
2001           2888 0201 
2021          38888 0101 
2021          37778 0101 
2021          28888 0201 

-- 每月至今收入最低的机构
SELECT year,month,revenue_total,
LAST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) fr
FROM finance_info;
YEAR  MONTH REVENUE_TOTAL FR    
----- ----- ------------- ----- 
2021  01            38888 0101 
2021  02            37778 0101 
2021  01            28888 0201 
2001  01             2888 0201    

-- 每月在三个月内收入最低的机构
SELECT year,month,revenue_total,
LAST_VALUE(branch) OVER (ORDER BY revenue_total DESC ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) fr
FROM finance_info;
YEAR  MONTH REVENUE_TOTAL FR    
----- ----- ------------- ----- 
2021  01            38888 0101 
2021  02            37778 0201 
2021  01            28888 0201 
2001  01             2888 0201  
```
