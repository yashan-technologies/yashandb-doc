```ebnf+diagram
timediff::= TIMEDIFF "(" expr1 "," expr2 ")" 
```

TIMEDIFF函数用于计算expr1-expr2的差值，返回值为TIME类型。

**expr1/expr2**

- expr1和expr2为YashanDB认可的[通用表达式](../通用SQL语法/expr)，必须可转换为TIME类型。
- 当expr1和expr2都为日期时间型但并非同一子类型时，函数返回NULL。
- 当expr1为NULL，或expr1为合法参数且expr2为NULL时，函数返回NULL。

示例（单机HEAP表）

```sql
-- time类型
CREATE TABLE time_time_diff(C1 TIME, C2 TIME);

INSERT INTO time_time_diff VALUES('11:37:10', '10:20:09');

SELECT TIMEDIFF(C1, C2) res FROM time_time_diff;

RES                  
-------------------------------- 
01:17:01.000000

-- date类型
CREATE TABLE date_date_diff(C1 DATE, C2 DATE);

INSERT INTO date_date_diff VALUES('2022-12-10', '2022-11-24');

SELECT TIMEDIFF(C1, C2) res FROM date_date_diff;

RES
--------------------
128:00:00.000000

-- timestamp类型
CREATE TABLE timestamp_timestamp_diff(C1 TIMESTAMP, C2 TIMESTAMP);

INSERT INTO timestamp_timestamp_diff VALUES('2022-11-24 11:53:10', '2022-12-10 09:12:49');

SELECT TIMEDIFF(C1, C2) res FROM timestamp_timestamp_diff;

RES               
-------------------------------- 
+714 02:40:21.000000

```
