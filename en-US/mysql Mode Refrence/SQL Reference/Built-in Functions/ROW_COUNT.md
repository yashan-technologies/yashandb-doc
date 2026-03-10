```ebnf+diagram
row_count::= ROW_COUNT["()"]
```

The ROW_COUNT function returns the statistics of the last executed statement.

This function follows the rules below:

- If the last statement is a DDL such as CREATE, DROP, or ALTER TABLE, the function returns 0. However, for CREATE TABLE .. AS SELECT statements, it returns the number of rows already present in the table after creation.
- If the last statement is a DML such as UPDATE, INSERT, DELETE statements, or a LOAD DATA statement on the server (excluding SELECT), the function returns the number of affected rows. For UPDATE statements with a WHERE clause, it returns the number of rows matched by the WHERE condition.
- If the last statement is a SELECT statement, the function always returns -1.

***Example*** for Standalone Deployment Heap tables

```sql

DROP TABLE IF EXISTS mysql_json_rowcount_tb;


SELECT ROW_COUNT() res FROM dual;

                  res 
--------------------- 
                    0

CREATE TABLE mysql_json_rowcount_tb (c1 INTEGER);


SELECT ROW_COUNT() res FROM dual;

                  res 
--------------------- 
                    0

INSERT INTO mysql_json_rowcount_tb VALUES(1);


SELECT ROW_COUNT() res FROM dual;

                  res 
--------------------- 
                    1

INSERT INTO mysql_json_rowcount_tb VALUES(2),(3);


SELECT ROW_COUNT() res FROM dual;

                  res 
--------------------- 
                    2
```
