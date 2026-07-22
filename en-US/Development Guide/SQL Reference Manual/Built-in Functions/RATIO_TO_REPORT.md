```ebnf
ratio_to_report = RATIO_TO_REPORT "(" expr ")" OVER "(" [query_partition_clause]")".
```

RATIO_TO_REPORT is a [window function](00Built-in Functions.md#WindowFunction) used to calculate the ratio of a value to the total sum of all values within a group.

This function does not support vectorization calculation.

**expr**

The expression needed to calculate the ratio, usually a column or a computed value.

- expr can be NUMBER, SMALLINT, INT, BIGINT, TINYINT numeric types and floating-point types (FLOAT, DOUBLE).
- expr can be a character type whose value can be converted to a numeric type; if the character type value cannot be converted to a numeric type, an error will occur.
- expr cannot be a date type (DATE, TIME, TIMESTAMP, INTERVAL YEAR TO MONTH, INTERVAL DAY TO SECOND), RAW type, BOOLEAN type, BIT type, BFILE type, large object types (CLOB, BLOB, NCLOB), ROWID/UROWID types, or JSON types.
- expr can be a built-in function type, but cannot be an analytical type function such as a window function.

**query\_partition\_clause**

This clause is used for partition calculations and is optional. If omitted, RATIO_TO_REPORT will calculate over the entire result set.

General syntax for window functions.

***Example*** for Heap tables

```sql
-- Sales_info table data preparation
INSERT INTO sales_info VALUES ('2023','12','0102','10001',20,2000,'');
INSERT INTO sales_info VALUES ('2024','03','0102','10001',10,1000,'');
INSERT INTO sales_info VALUES ('2024','05','0101','10001',40,4000,'');
COMMIT;
SELECT product, branch, amount FROM sales_info;

PRODUCT           BRANCH         AMOUNT
----------------- --------- -----------
11001             0402              300
11001             0201              500
11001             0102              300
11001             0102              300
10001             0102             2000
10001             0102             1000
11001             0101              600
11001             0101              300
10001             0101             4000

-- No partition calculation
SELECT product, branch, amount, ROUND(RATIO_TO_REPORT(amount) OVER(), 8) AS sales_ratio FROM sales_info;

PRODUCT           BRANCH         AMOUNT SALES_RATIO
----------------- --------- ----------- -----------
11001             0402              300   .03225806
11001             0201              500   .05376344
11001             0102              300   .03225806
11001             0102              300   .03225806
10001             0102             2000   .21505376
10001             0102             1000   .10752688
11001             0101              600   .06451613
11001             0101              300   .03225806
10001             0101             4000   .43010753

-- Partition calculation by branch
SELECT product, branch, amount, ROUND(RATIO_TO_REPORT(amount) OVER(PARTITION BY branch), 8) AS sales_ratio FROM sales_info;

PRODUCT           BRANCH         AMOUNT SALES_RATIO
----------------- --------- ----------- -----------
11001             0101              600   .12244898
11001             0101              300   .06122449
10001             0101             4000   .81632653
11001             0102              300   .08333333
11001             0102              300   .08333333
10001             0102             2000   .55555556
10001             0102             1000   .27777778
11001             0201              500           1
11001             0402              300           1
```
