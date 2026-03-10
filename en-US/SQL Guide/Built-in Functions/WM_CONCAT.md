```ebnf+diagram
wm_concat::= WM_CONCAT "(" [DISTINCT|ALL] string ")" [ OVER "(" [query_partition_clause] [order_by_clause] ")" ]
```

The WM_CONCAT function concatenates multiple rows of data, separating them with a delimiter (`,`) and returning a single row of CLOB type string.

This function is similar to the [GROUP_CONCAT](GROUP_CONCAT) function in functionality, but differs in that WM_CONCAT does not allow customization of the SEPARATOR delimiter and does not permit ORDER BY sorting.

**DISTINCT**

When calculating the final concatenated result, it filters out duplicate rows that appear within the same group.


DISTINCT does not support LOB data.


**ALL**

Default value, indicating that duplicate rows are not filtered, and all rows are concatenated.

**string**

The string can be a general expression [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) or a subquery returning a single row of data from a single column. The value of string must be character types, or other types that can be converted to character types, except for JSON and timezone types.

- In the vectorized execution engine, string cannot be of type LOB for external storage data.

- In single row calculations, when the value of string is NULL, the function returns NULL.

- In multi-row calculations, the function will ignore rows where the string value is empty, and if all rows are empty, the result will be NULL. When there are multiple concatenated rows, the results will be separated by `,`.

***Example***

```sql
-- Create exprs_wmconcat table and insert data
CREATE TABLE exprs_wmconcat (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_wmconcat 
VALUES  (1,'Dong',10000),(2,'Ming',46450),
        (3,'Hong',46450 ),(4,'Dong',14465),
        (5,'Ming',46450),(6,'Dong',46450);

-- When GROUP BY is not specified, all rows are CONCATed to get a single row of results
SELECT WM_CONCAT(MONEY) AS money FROM exprs_wmconcat;
MONEY
----------------------------------------------------------------
1.0E+004,4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004

-- After grouping, the multi-row data in each group is concatenated into a single row, resulting in multi-row results by group
SELECT NAME,WM_CONCAT(MONEY) AS money FROM exprs_wmconcat GROUP BY name;
NAME                 MONEY
-------------------- ----------------------------------------------
Dong               1.0E+004,1.4465E+004,4.645E+004
Ming               4.645E+004,4.645E+004
Hong               4.645E+004

-- Use DISTINCT keyword to remove duplicates
SELECT WM_CONCAT(DISTINCT name) AS names FROM exprs_wmconcat;
NAMES
------------------------
Dong,Ming,Hong
```

**OVER**

When the OVER keyword is specified, WM_CONCAT will act as a [window function](00Built-in Functions.html#WindowFunction), returning the sum of multiple rows' values, with the return type as CLOB.

When WM_CONCAT is used as a window function, string cannot be a time type.

**query_partition_clause**

Generic syntax for window functions.

**order_by_clause**

Generic syntax for window functions.

***Example***

```sql
CREATE TABLE tb_wm_concat_fruit(id INT,name VARCHAR(100), num INT);
INSERT INTO tb_wm_concat_fruit VALUES(1,'apple', 11);
INSERT INTO tb_wm_concat_fruit VALUES(2,'orange', 22);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 33);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 44);
INSERT INTO tb_wm_concat_fruit VALUES(3,'banana', 55);
INSERT INTO tb_wm_concat_fruit VALUES(4,'cream', 66);
INSERT INTO tb_wm_concat_fruit VALUES(4,'cream', 77);
COMMIT;

SELECT id,WM_CONCAT(name) OVER (PARTITION BY id) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana,banana,banana
           3 banana,banana,banana
           3 banana,banana,banana
           4 cream,cream
           4 cream,cream


SELECT id,WM_CONCAT(DISTINCT name) OVER (PARTITION BY id) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana
           3 banana
           3 banana
           4 cream
           4 cream


SELECT id,WM_CONCAT(name) OVER (PARTITION BY id ORDER BY num) AS fruit_name FROM tb_wm_concat_fruit;

          ID FRUIT_NAME
------------ ----------------------------------------------------------------
           1 apple
           2 orange
           3 banana
           3 banana,banana
           3 banana,banana,banana
           4 cream
           4 cream,cream

```
