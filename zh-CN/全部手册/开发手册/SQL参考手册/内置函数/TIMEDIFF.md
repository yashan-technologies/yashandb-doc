```ebnf+diagram
TIMEDIFF::= TIMEDIFF "(" expr1 "," expr2 ")" 
```

TIMEDIFF函数用于计算expr1与expr2之间的时间差，返回一个INTERVAL DAY TO SECOND类型的数值。

**expr1/expr2**

- expr1和expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr)，并且类型相同，即TIMESTAMP、DATE、TIME类型或可以转换为TIMESTAMP、DATE、TIME类型的字符型。
- 当expr1和expr2都不为字符型，且expr1和expr2类型不同时，则报错。
- 当其中一个参数为TIMESTAMP、DATE、TIME类型，另一个参数为字符型时，则将字符串类型的参数转换成与另一个参数类型相同的时间日期类型。
- 当expr1和expr2都为字符型时，则将expr1和expr2都转成TIMESTAMP类型。
- 当expr1或者expr2的值为NULL时，函数返回NULL。

示例

```sql
-- time类型
CREATE TABLE time_time_diff(C1 TIME, C2 TIME);

INSERT INTO time_time_diff VALUES('11:37:10', '10:20:09');

SELECT TIMEDIFF(C1, C2) res FROM time_time_diff;

RES                  
-------------------------------- 
+00 01:17:01.000000             

-- date类型
CREATE TABLE date_date_diff(C1 DATE, C2 DATE);

INSERT INTO date_date_diff VALUES('2022-11-24', '2020-12-10');

SELECT TIMEDIFF(C1, C2) res FROM date_date_diff;

RES                 
-------------------------------- 
+714 00:00:00.000000            

-- timestamp类型
CREATE TABLE timestamp_timestamp_diff(C1 TIMESTAMP, C2 TIMESTAMP);

INSERT INTO timestamp_timestamp_diff VALUES('2022-11-24 11:53:10', '2020-12-10 09:12:49');

SELECT TIMEDIFF(C1, C2) res FROM timestamp_timestamp_diff;

RES               
-------------------------------- 
+714 02:40:21.000000

```
