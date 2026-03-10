```ebnf+diagram
lnnvl::= LNNVL "(" condition ")"
```

LNNVL函数用于计算条件，尤其适用于计算存在操作数为NULL的条件。

本函数通常在WHERE语句中使用，也可以作为搜索表达式中的条件，在这类语句中，普通的条件判断对NULL操作数返回false或者未知，从而导致空数据被过滤，使用LNNVL函数则可以解决这个问题，减少用户还需进行IS NULL判断的操作处理。

**condition**

YashanDB支持的条件语句，具体参考通用SQL语法[condition](../../全部手册/开发手册/SQL参考手册/通用SQL语法/condition)。

当condition的结果为false或者未知时，函数返回true；当condition的结果为true时，函数返回false。

示例

```sql
-- LNNVL函数使用效果
SELECT LNNVL(1 IN (2,3)) res FROM DUAL;
RES                  
-------------------- 
true    

SELECT LNNVL('' IS NOT NULL) res FROM DUAL;
RES                  
-------------------- 
true   

-- 普通条件对NULL判断返回false，从而过滤掉空数据
-- area1表共有5条数据，EMPLOYEE_COUNT为空的业务含义为0
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME                DHQ                   EMPLOYEE_COUNT 
------- ------------------------ --------------------- -------------- 
01      EastChina                   Shanghai                            
02      WestChina                   Chengdu                          300
03      SouthChina                   Guangzhou                        400
04      NorthChina                   Beijing                          300
05      CentralChina                   Wuhan                               

-- 当统计EMPLOYEE_COUNT值小于400的区域数时，由于空值被过滤导致结果与实际业务不符，除非还进行IS NULL判断
SELECT COUNT(1) res FROM area1 WHERE employee_count<400;
                  RES 
--------------------- 
                    2

SELECT COUNT(1) res FROM area1 WHERE employee_count<400 OR employee_count IS NULL;
                  RES 
--------------------- 
                    4
                    
-- 使用LNNVL函数可以替代上条语句
SELECT COUNT(1) res FROM area1 WHERE LNNVL(employee_count>=400);
                  RES 
--------------------- 
                    4
```
