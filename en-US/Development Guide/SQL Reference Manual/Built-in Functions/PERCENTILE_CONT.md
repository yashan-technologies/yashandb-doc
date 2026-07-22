```ebnf
percentile_cont = PERCENTILE_CONT "(" percentile ")" WITHIN GROUP "(" ORDER BY expr [DESC|ASC] [NULLS FIRST|LAST] ")"  [OVER query_partition_clause].
```

The PERCENTILE_CONT function assumes that the expression [expr](../General SQL Syntax/expr) is continuously distributed. It sorts the data within the group, ignores rows where the data is null, and returns the interpolation corresponding to the given percentile value. When the specified percentile is 0.5, the result returned is the median of the sorted keys within the group.

When the OVER keyword syntax is not specified, this function is an aggregate function.

This function does not support vectorization calculation.

**percentile**

The percentile value is only allowed to be numeric types and other data types that can be implicitly converted to numeric types.

The range of percentile values is [0, 1], otherwise an error will be reported.

The percentile must be a constant within each aggregation group.

**expr**

Specifies the function's sorting key within the group.

The expr is only allowed to be numeric types or time types.

When the type of expr is a non-floating-point numeric type, the function returns a number type. If expr is a time type or floating-point type, the function returns the same type as expr.

**Function Linear Interpolation Algorithm**

Sort the sorting key to obtain the ordered data. Let the given percentile value be P, and the number of non-null data rows within the group be N. RowNum is calculated using the formula RowNum = 1 + P * ( N - 1). FloorRowNum is obtained by rounding down RowNum, and CeilRowNum is obtained by rounding up RowNum. The final result of the function has the following conditions:

- If FloorRowNum = RowNum = CeilRowNum, the result is the value of the sorting key at the RowNum-th row in the sorted order within the group.

- If the three are not equal, let the value of the sorting key at the FloorRowNum-th row in the group be FloorValue, and the value of the sorting key at the CeilRowNum-th row be CeilValue. The result is then FloorValue + (RowNum - FloorRowNum) * (CeilValue - FloorValue).

**DESC|ASC and NULLS FIRST|LAST**

The sorting key can be specified for ascending or descending order, where ASC stands for ascending and DESC for descending, with the default being ascending.

NULLS FIRST and NULLS LAST indicate placing NULLs at the beginning and end of the group, respectively. By default, NULLs are placed at the maximum position, so in ascending order (ASC), the default is NULLS LAST; while in descending order (DESC), it defaults to NULLS FIRST.

***Example*** for Heap tables

```sql
-- Create the exprs table and insert data
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , data int, key_group int);
INSERT INTO exprs VALUES(1,3, 1);
INSERT INTO exprs VALUES(2,1, 1);
INSERT INTO exprs VALUES(3,2, 1);
INSERT INTO exprs VALUES(4,4, 2);
COMMIT;
-- Calculate the median of data (i.e., when the percentile is 0.5)
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) res FROM exprs;
RES
----------------------------------------------------------------
2.5

-- Calculate the interpolation when the percentile of data is 40%
SELECT PERCENTILE_CONT(0.4) WITHIN GROUP (ORDER BY data) res FROM exprs;
RES
----------------------------------------------------------------
2.2

-- Use group by for grouping and calculate the median for each group
SELECT key_group, PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) res FROM exprs group by key_group;
    KEY_GROUP         RES
------------ -----------
           1           2
           2           4
```

**OVER**

When the OVER keyword is specified, PERCENTILE_CONT will act as a [window function](00Built-in Functions.md#WindowFunction). It will sort the rows in each grouped window according to the sorting rule and calculate the interpolation corresponding to the given percentile. The result for each row in the grouped window will be the same.

**query\_partition\_clause**

General syntax for window functions.

***Example*** for Heap tables

```sql
-- Create the exprs table and insert data
DROP TABLE IF EXISTS exprs;
CREATE TABLE exprs(id INT , data int, key_group int);
INSERT INTO exprs VALUES(1,3, 1);
INSERT INTO exprs VALUES(2,1, 1);
INSERT INTO exprs VALUES(3,2, 1);
INSERT INTO exprs VALUES(4,4, 2);
COMMIT;

-- Window function calculates the median in each window
SELECT id,data,key_group,
PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY data) OVER (PARTITION BY key_group) c1
FROM exprs order by id;
           ID         DATA    KEY_GROUP          C1
------------ ------------ ------------ -----------
           1            3            1           2
           2            1            1           2
           3            2            1           2
           4            4            2           4
```
