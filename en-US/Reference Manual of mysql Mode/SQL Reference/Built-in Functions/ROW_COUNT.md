```ebnf
row_count = ROW_COUNT["()"].
```

The ROW_COUNT function returns the statistics of the last executed statement.

This function follows the rules below:

- If the last statement is a DDL such as CREATE, DROP, or ALTER TABLE, the function returns 0. However, for CREATE TABLE .. AS SELECT statements, it returns the number of rows already present in the table after creation.
- If the last statement is a DML such as UPDATE, INSERT, DELETE statements, or a LOAD DATA statement on the server (excluding SELECT), the function returns the number of affected rows. For UPDATE statements with a WHERE clause, it returns the number of rows matched by the WHERE condition.
- If the last statement is a SELECT statement, the function always returns -1.

***Example*** for  Heap tables

```sql

drop table if exists mysql_json_rowcount_tb;

Succeed.

select row_count() res from dual;

                  res 
--------------------- 
                    0

create table mysql_json_rowcount_tb (c1 integer);

Succeed.

select row_count() res from dual;

                  res 
--------------------- 
                    0

insert into mysql_json_rowcount_tb values(1);

1 row affected.

select row_count() res from dual;

                  res 
--------------------- 
                    1

insert into mysql_json_rowcount_tb values(2),(3);

2 rows affected.

select row_count() res from dual;

                  res 
--------------------- 
                    2
```
