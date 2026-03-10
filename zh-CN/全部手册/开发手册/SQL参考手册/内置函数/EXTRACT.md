```ebnf+diagram
extract::= EXTRACT "(" (YEAR|MONTH|DAY|HOUR|MINUTE|SECOND) FROM expr ")"
```

EXTRACT函数对给定参数[expr](../通用SQL语法/expr)进行年、月、日、小时、分、秒等数值的提取，其返回值类型有以下几种情况：

*   当expr值为NULL时，返回NULL。
*   当expr值的数据类型不为DATE、TIMESTAMP、时区、TIME、INTERVAL DAY TO SECOND、INTERVAL YEAR TO MONTH时，返回类型不符合预期。
*   expr值的数据类型与年、月、日、小时、分、秒的指定存在如下对应关系，其中出现N/A时返回Illegal format错误，否则按最后一列类型返回：

|  Field| TIMESTAMP| 时区| TIME| DATE| INTERVAL DAY TO SECOND| INTERVAL YEAR TO MONTH| 返回类型|
| --- | --- | --- | --- |--- | --- | --- | --- |
| DAY | 一个月中的日 <br>(1-31) | 一个月中的日 <br>(1-31) | N/A | 一个月中的日 <br/>(1-31) | 天数  | N/A | INT |
| HOUR | 一天中的小时 <br/>(0-23) | 一天中的小时 <br/>(0-23) | 一天中的小时<br/> (0-23) | N/A | 一天中的小时<br/> (0-23) | N/A | INT |
| MINUTE | 一小时中的分<br/> (0-59) | 一小时中的分<br/> (0-59) | 一小时中的分<br/> (0-59) | N/A | 一小时中的分<br/> (0-59) | N/A | INT |
| MONTH | 一年中的月 <br/>(1-12) | 一年中的月 <br/>(1-12) | N/A | 一年中的月<br/> (1-12) | N/A | 月数  | INT |
| SECOND | 一分钟的秒<br/>(0-59.999999) | 一分钟的秒<br/>(0-59.999999) | 一分钟的秒<br/>(0-59.999999) | N/A | 一分钟的秒<br/>(0-59.999999) | N/A | NUMBER |
| YEAR | 年份  | 年份  | N/A | 年份  | N/A | 年数  | INT |

示例

```sql
-- 创建times表，包含DATE、TIMESTAMP、TIME、INTERVAL DAY TO SECOND、INTERVAL YEAR TO MONTH类型的列字段
CREATE TABLE times (timea DATE DEFAULT SYSDATE,
timeb TIMESTAMP DEFAULT SYSDATE,
timec TIME DEFAULT SYSDATE,
timed INTERVAL DAY TO SECOND,
timee INTERVAL YEAR TO MONTH);
INSERT INTO times VALUES (DEFAULT,DEFAULT,DEFAULT,INTERVAL '5' DAY,INTERVAL '2' YEAR);
COMMIT;
 
SELECT timea,timeb,timec,timed,timee FROM times;
TIMEA                 TIMEB                        TIMEC             TIMED                TIMEE 
--------------------- ---------------------------- ----------------- -------------------- ------
2022-01-17 20:47:19   2022-01-17 20:47:19.000000   20:47:19.000000   +05 00:00:00.000000  +02-00
 
SELECT EXTRACT(YEAR FROM timea) Year,
EXTRACT(SECOND FROM timeb) Second,
EXTRACT(HOUR FROM timec) Hour,
EXTRACT(DAY FROM timed) Day,
EXTRACT(MONTH FROM timee) Month
FROM times;
        YEAR      SECOND         HOUR          DAY        MONTH
------------ ----------- ------------ ------------ ------------
        2022          19           20            5            0
```
