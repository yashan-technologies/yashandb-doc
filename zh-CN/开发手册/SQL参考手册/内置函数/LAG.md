```ebnf
lag = LAG "("expr [(RESPECT|IGNORE) NULLS] "," offset ["," default] ")" OVER "(" [query_partition_clause] order_by_clause ")".
```

LAG为[窗口函数](00内置函数.md#WindowFunction)。本函数根据窗口查询返回的一系列行和游标的位置，提供对位于该位置之前的给定物理偏移量处的行的访问，并返回该行记录对应[expr](../通用SQL语法/expr)的值。

窗口函数不可嵌套，因此expr为除窗口函数之外的其他通用表达式，其数据类型可以是除大对象型、JSON及UDT以外的数据类型。

**(RESPECT|IGNORE) NULLS**

指定expr的空值是否被包括在函数的计算中，缺省为RESPECT NULLS。

**offset**

指定物理偏移量的表达式，offset为与expr相同的通用表达式，须为除大对象型、JSON以外可转换为NUMBER的其他类型数据，但不能为NULL。

- offset可省略，则其默认值为1。

- 如果偏移量超出表的范围，则返回default定义的默认值。

**default**

指定按给定偏移量无法获取到行记录时，函数返回的默认值。default为与expr相同的通用表达式，其数据类型可以是除大对象型、JSON及UDT以外的数据类型，可省略，则默认值为NULL。

当default与expr的类型不一致时，则将default向expr类型进行转换，长度取default与expr中最大长度，如果转换失败则报错。

示例

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

-- 返回AREA_NO排序上一行的EMPLOYEE_COUNT
SELECT LAG(employee_count) OVER(order by AREA_NO) lag1 FROM area1;
               LAG1
-------------------
               
               
               300
               400
               300

-- 返回AREA_NO排序上一个非空的EMPLOYEE_COUNT
SELECT LAG(employee_count IGNORE NULLS) OVER(order by AREA_NO) lag2 FROM area1;
               LAG2
-------------------
               
               
               300
               400
               300

-- 返回AREA_NO排序上一个非空的EMPLOYEE_COUNT,若为空则返回0
SELECT LAG(employee_count IGNORE NULLS,1,0) OVER(order by AREA_NO) lag3 FROM area1;
               LAG3
-------------------
               0
               0
               300
               400
               300
```

**query\_partition\_clause**|**order\_by\_clause**

窗口函数通用语法。

示例

```sql
-- finance_info表记录了分年、月、机构的收入情况
SELECT year,month,branch,revenue_total FROM finance_info;
YEAR  MONTH BRANCH REVENUE_TOTAL 
----- ----- ------ ------------- 
2001  01    0201            2888
2021  01    0201           28888
2021  01    0101           38888
2021  02    0101           37778

-- 分年统计每一个机构每月和上月的收入对比情况
SELECT year,month,revenue_total curr_month,
LAG(revenue_total,1,0) OVER (PARTITION BY year ORDER BY year,month,branch) last_month
FROM finance_info;
YEAR  MONTH  CURR_MONTH  LAST_MONTH 
----- ----- ----------- ----------- 
2001  01           2888           0
2021  01          38888           0
2021  01          28888       38888
2021  02          37778       28888 
```
