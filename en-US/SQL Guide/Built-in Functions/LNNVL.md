```ebnf+diagram
lnnvl::= LNNVL "(" condition ")"
```

The LNNVL function is used to evaluate conditions, especially for evaluating conditions involving NULL operands.

This function is commonly used in WHERE statements, and can also be used as a condition in search expressions. In such statements, regular condition evaluations return false or unknown for NULL operands, leading to data being filtered out. The use of the LNNVL function can resolve this issue, reducing the need for users to perform IS NULL checks.

**condition**

Conditions supported by YashanDB are detailed in the general SQL syntax [condition](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/condition).

When the result of the condition is false or unknown, the function returns true; when the result of the condition is true, the function returns false.

***Example***

```sql
-- Effect of using the LNNVL function
SELECT LNNVL(1 IN (2,3)) res FROM DUAL;
RES                  
-------------------- 
true    

SELECT LNNVL('' IS NOT NULL) res FROM DUAL;
RES                  
-------------------- 
true   

-- Regular condition's evaluation of NULL returns false, filtering out empty data
-- The area1 table has a total of 5 records, where the business meaning of EMPLOYEE_COUNT being NULL is 0
SELECT area_no,area_name,DHQ,employee_count FROM area1;
AREA_NO AREA_NAME                DHQ                   EMPLOYEE_COUNT 
------- ------------------------ --------------------- -------------- 
01      EastChina                   Shanghai                            
02      WestChina                   Chengdu                          300
03      SouthChina                   Guangzhou                        400
04      NorthChina                   Beijing                          300
05      CentralChina                   Wuhan                               

-- When counting the number of regions with EMPLOYEE_COUNT less than 400, the result is inconsistent with actual business due to the filtering of NULL values, unless an IS NULL check is also performed
SELECT COUNT(1) res FROM area1 WHERE employee_count<400;
                  RES 
--------------------- 
                    2

SELECT COUNT(1) res FROM area1 WHERE employee_count<400 OR employee_count IS NULL;
                  RES 
--------------------- 
                    4
                    
-- Using the LNNVL function can replace the previous statement
SELECT COUNT(1) res FROM area1 WHERE LNNVL(employee_count>=400);
                  RES 
--------------------- 
                    4
```
