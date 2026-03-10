```ebnf+diagram
row_count::= ROW_COUNT["()"]
```

ROW_COUNT函数返回上一条执行的语句的统计信息。

本函数遵循如下规则：

- 若上一条语句为CREATE/DROP/ALTER TABLE等DDL，函数返回0，但对于CREATE TABLE .. AS SELECT语句则返回建表后表中已有的行数。
- 若上一条语句为UPDATE、INSERT、DELETE语句或服务端的LOAD DATA语句等除SELECT之外的DML，函数返回受影响行的数量，但对于存在有where子句的UPDATE语句则返回where条件所匹配的行数。
- 若上一条语句为SELECT语句，函数固定返回-1。

示例（单机HEAP表）

```sql

DROP TABLE IF EXISTS mysql_json_rowcount_tb;


SELECT ROW_COUNT() FROM dual;

          row_count() 
--------------------- 
                    0

CREATE TABLE mysql_json_rowcount_tb (c1 INTEGER);


SELECT ROW_COUNT() FROM dual;

          row_count() 
--------------------- 
                    0

INSERT INTO mysql_json_rowcount_tb VALUES(1);


SELECT ROW_COUNT() FROM dual;

          row_count() 
--------------------- 
                    1

INSERT INTO mysql_json_rowcount_tb VALUES(2),(3);


SELECT ROW_COUNT() FROM dual;

          row_count() 
--------------------- 
                    2
```
