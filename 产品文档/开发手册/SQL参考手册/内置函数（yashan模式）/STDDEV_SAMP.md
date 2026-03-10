```ebnf+diagram
stddev_samp::= STDDEV_SAMP "(" expr ")"
```

STDDEV_SAMP函数计算[expr](../通用SQL语法/expr)的值的样本标准差。

当给定参数只有一行数据时，STDDEV_SAMP函数的计算结果为NULL。  

STDDEV_SAMP函数的返回值类型有以下几种情况：

*   当expr的值为TINYINT、SMALLINT、INT、BIGINT、NUMBER类型时，返回NUMBER类型。
*   当expr的值为FLOAT、DOUBLE类型时，返回与参数一致的FLOAT、DOUBLE类型。
*   当expr的值为NULL时，返回NULL。
*   当expr的值为字面量、变量、常量时，返回0。
*   当expr的值为其他类型且无法转换为NUMBER类型时，不执行计算并返回类型转换错误。

其中expr的值为数值型或可以转换为NUMBER类型的字符型。对于其他类型，函数返回类型不支持。

在多行计算中，系统忽略数值为空的行，当所有行均为空时，计算结果为NULL。



示例

```sql
-- area1表中包含如下字段和数据
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME          DHQ            EMPLOYEE_COUNT
------- ------------------ -------------- --------------
01      华东             Shanghai                       
02      华西             Chengdu                   300  
03      华南             Guangzhou                 400  
04      华北             Beijing                   300  
05      华中             Wuhan     
 
-- 计算员工数量的样本标准差,为空的行将被忽略
 
SELECT STDDEV_SAMP(employee_count) res FROM area1;
                 RES
--------------------
          57.7350269
 
-- 只有一行记录时，样本标准差为NULL
SELECT STDDEV_SAMP(employee_count) res FROM area1 WHERE area_no='02';
                 RES
--------------------
                    
```
