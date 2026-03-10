```ebnf+diagram
extract::= EXTRACT "(" (YEAR|QUARTER|MONTH|WEEK|DAY|HOUR|MINUTE|SECOND|MICROSECOND|SECOND_MICROSECOND|MINUTE_MICROSECOND|MINUTE_SECOND|HOUR_MICROSECOND|HOUR_SECOND|HOUR_MINUTE|DAY_MICROSECOND|DAY_SECOND|DAY_MINUTE|DAY_HOUR|YEAR_MONTH) FROM expr ")"
```

EXTRACT函数对给定参数[expr](../通用SQL语法/expr)进行年、月、日、小时、分、秒等数值的提取。

**expr**

expr的值必须为DATE、DATETIME、TIMESTAMP、TIME或可转为时间类型的其他类型。

*   当expr值为NULL时，返回NULL。

*   当expr为TIME类型，会使用当前的系统时间填充年月日。

*   当expr为DATE类型，会使用0填充时分秒。

*   当expr为DATETIME类型或TIMESTAMP类型，会直接使用输入的年月日时分秒信息，缺失则用0填充。

*   当expr不是时间类型时：

    *   若提取单位为YEAR、QUARTER、MONTH、WEEK、DAY或YEAR_MONTH时，则将expr转换为TIMESTAMP类型后进行处理。

    *   若提取单位为HOUR、MINUTE、SECOND、MICROSECOND、SECOND_MICROSECOND、MINUTE_MICROSECOND、MINUTE_SECOND、HOUR_MICROSECOND、HOUR_SECOND或HOUR_MINUTE，则将expr转换为time后进行处理。

    *   若提取单位为DAY_MICROSECOND、DAY_SECOND、DAY_MINUTE或DAY_HOUR：

        * 当expr为字符串时，若expr的有效数字为6位及以下则转换为TIME类型后进行处理；否则转换为TIMESTAMP类型进行处理。如果字符串中仅含一个小数点时，小数点前的数字认为是有效数字。

        * 当expr为数值型时，6位数及以下则将expr转换为TIME类型后进行处理；否则将expr转换为TIMESTAMP类型后进行处理。

        * 当expr为其他类型时，转换为TIMESTAMP类型后进行处理。

示例（单机HEAP表）

```sql
-- 创建times表，包含DATE、TIMESTAMP、TIME类型的列字段
CREATE TABLE times (timea DATE DEFAULT SYSDATE,
timeb TIMESTAMP DEFAULT SYSDATE,
timec TIME DEFAULT SYSDATE);
INSERT INTO times VALUES (DEFAULT,DEFAULT,DEFAULT);
COMMIT;

-- example via yasql
SELECT timea,timeb,timec FROM times;
timea                            timeb                                                            timec
-------------------------------- ---------------------------------------------------------------- --------------------
2025-03-18 14:54:19              2025-03-18 14:54:19.000000                                       14:54:19.000000

-- example via MySQL Client
SELECT timea,timeb,timec FROM times;
+------------+---------------------+----------+
| timea      | timeb               | timec    |
+------------+---------------------+----------+
| 2025-10-16 | 2025-10-16 14:08:33 | 14:08:33 |
+------------+---------------------+----------+

SELECT timea,timeb,timec FROM times;
+------------+---------------------+----------+
| timea      | timeb               | timec    |
+------------+---------------------+----------+
| 2025-10-16 | 2025-10-16 14:08:33 | 14:08:33 |
+------------+---------------------+----------+
 
SELECT EXTRACT(YEAR FROM timea) Year,
EXTRACT(MONTH FROM timeb) Month,
EXTRACT(DAY FROM timec) Day,
EXTRACT(HOUR FROM timea) Hour,
EXTRACT(SECOND FROM timeb) Second
FROM times;
                Year                 Month                   Day                  Hour                 Second
--------------------- --------------------- --------------------- --------------------- ---------------------
                 2025                     3                    18                    14                    19

SELECT EXTRACT(YEAR FROM 123) Year,
       EXTRACT(MONTH FROM 123) Month,
       EXTRACT(DAY FROM 123) Day,
       EXTRACT(HOUR FROM 123) Hour,
       EXTRACT(SECOND FROM 123) Second
FROM DUAL;
                Year                 Month                   Day                  Hour                 Second
--------------------- --------------------- --------------------- --------------------- ---------------------
                 2000                     1                    23                     0                    23
```
