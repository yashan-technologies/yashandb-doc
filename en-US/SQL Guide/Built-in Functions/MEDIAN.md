```ebnf+diagram
median::= MEDIAN "(" [ALL] expr ")" [ OVER "(" [query_partition_clause] ")" ]
```

The MEDIAN function calculates the median of the given parameter [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr). It only supports expr types of [numeric](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/Numeric Types) or [date-time](../../All Manuals/Development Guide/SQL Reference Manual/Data Types/Date-Time Types). Using other data types will result in an error.

This function does not support vectorization calculation.

The MEDIAN window function cannot be used with DISTINCT or ORDER BY statements.

Function return value types:

|Data Type |Return Value Type |
| --------- | ---------- |
| TINYINT   | NUMBER     |
| SMALLINT  | NUMBER     |
| INT       | NUMBER     |
| BIGINT    | NUMBER     |
| FLOAT     | FLOAT      |
| DOUBLE    | DOUBLE     |
| NUMBER    | NUMBER     |
| DATE      | DATE       |
| TIMESTAMP | TIMESTAMP  |
| TIME      | TIME       |
| INTERVAL  | INTERVAL   |

In single row calculations, when the value of expr is null, the function returns NULL; if expr is a literal NULL, it will return an error. If expr uses a bind parameter and the bind is a literal NULL, it returns result NULL.

In multi-row calculations, the function will ignore rows where the expr value is null. When all rows are null, the calculation result is NULL.

**ALL**

Indicates that the median is calculated over all rows.

**query_partition_clause**

The general syntax of window functions.

Expr only supports INT, NUMBER, and time types. The parameters for partition by support other data types except for LOB, JSON, and UDT.

***Example*** for Heap tables

```sql
-- Create table exam and insert data
CREATE TABLE exam(id INT,score INT);
INSERT INTO exam VALUES(1,99);
INSERT INTO exam VALUES(2,80);
INSERT INTO exam VALUES(3,80);

SELECT id, score FROM exam;
          ID        SCORE
------------ ------------
           1           99
           2           80
           3           80

SELECT MEDIAN(score) res FROM exam;
         RES
------------
         80

SELECT MEDIAN(score) OVER () res FROM exam;
                RES
-------------------
                 80
                 80
                 80

SELECT MEDIAN(score) OVER (PARTITION BY id) res FROM exam;
                 RES
--------------------
                  99
                  80
                  80

SELECT MEDIAN(null) OVER() FROM sys.dual;
[1:15]YAS-04322 invalid datatype
```
