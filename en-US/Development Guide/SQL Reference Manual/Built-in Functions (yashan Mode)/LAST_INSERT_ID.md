```ebnf+diagram
last_insert_id::= LAST_INSERT_ID ["(" [expr] ")"]
```

The LAST_INSERT_ID function is used to obtain the latest auto-increment value in the current session or to convert the parameter [expr](../General SQL Syntax/expr) to BIGINT type.

This function does not support vectorization calculation.

**Obtaining Auto-increment Value**

When the LAST_INSERT_ID function has no parameters, it is used to retrieve the latest auto-increment value in the current session. The concepts related to auto-increment are as follows:

- Auto-increment Column: The first indexed column in the table with a default value of sequence. When it is a composite index, this column must be the first column declared in the index. Each table can have at most one auto-increment column.

- Auto-increment: Triggered when an INSERT statement is executed without explicitly inserting into the auto-increment column.

- Auto-increment Value: The initial auto-increment value for each session is 0. After an auto-increment has been triggered in the current session, the value is determined as follows:

  - If the last executed INSERT statement inserts multiple rows, the auto-increment value = the value of the auto-increment column corresponding to the first row of successfully inserted data.

  - If the last executed INSERT statement inserts 1 row, the auto-increment value = the value of the auto-increment column after the row is successfully inserted.

LAST_INSERT_ID() returns the latest auto-increment value of the current session, with a return type of BIGINT. If an overflow occurs, it returns 0.

**Type Conversion**

When the LAST_INSERT_ID function has one parameter expr, it is used to convert the parameter to BIGINT type and output it, while updating the current session's auto-increment value to the return value of LAST_INSERT_ID(expr) or 0.

- expr cannot be of UDT type.

- If expr fails to convert to BIGINT type, LAST_INSERT_ID(expr) returns 0.

- If expr is NULL, LAST_INSERT_ID(expr) returns NULL and the current session's auto-increment value is reset to the initial value 0. That is, after executing LAST_INSERT_ID(NULL) and then executing LAST_INSERT_ID(), the return value will be 0.

***Example*** for Heap tables

```sql
-- In the session, the initial value is 0
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
0

SELECT LAST_INSERT_ID(100) res FROM DUAL;

RES
---------------------
100

-- The parameterless query is affected by the parameterized query, returning the result from the last parameterized value
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
100

SELECT LAST_INSERT_ID(NULL) res FROM DUAL;

RES
---------------------

-- When the parameter is NULL, it returns NULL, followed by the parameterless query which will return 0
SELECT LAST_INSERT_ID() res FROM DUAL;

RES
---------------------
0

CREATE SEQUENCE seq_area;
CREATE TABLE area_info (id INT DEFAULT seq_area.NEXTVAL PRIMARY KEY, area_name VARCHAR(20));
INSERT INTO area_info(area_name) VALUES('Guangdong');
INSERT INTO area_info(area_name) VALUES('Zhejiang'),('Shanxi');
COMMIT;

SELECT id, area_name FROM area_info;
          ID AREA_NAME
------------ ---------------------
           1 Guangdong
           2 Zhejiang
           3 Shanxi

-- When inserting multiple rows, it returns the corresponding value of the first row that triggered auto-increment
SELECT LAST_INSERT_ID() RES FROM DUAL;

RES
---------------------
2
```
