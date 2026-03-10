```ebnf+diagram
any_value::= ANY_VALUE "(" expr ")"
```

The ANY_VALUE function is used to optimize queries that contain a GROUP BY clause and can return an indeterminate value for the given parameter [expr](../General SQL Syntax/expr). In cases where the statement fails due to the query column being unrelated to the columns specified in GROUP BY, expr can be assigned to this column name, and the function will return the first row of data.

This function follows the rules below:

- This function is used as an aggregate function.

- If the statement querying column data from the table does not contain a GROUP BY clause, this function has no effect.

- The function determines the return value data type based on the following rules:

    - If expr is of type TINYINT or SMALLINT, the function returns an INT type.

    - If expr is of type INT, the function returns a BIGINT type.
  
    - If expr is of type TINYINT UNSIGNED or SMALLINT UNSIGNED, the function returns an INT UNSIGNED type.
  
    - If expr is of type INT UNSIGNED or BIGINT UNSIGNED, the function returns a BIGINT UNSIGNED type.

    - If expr is of type CHAR, the function returns a VARCHAR type.

    - If expr is of any other type, the return value type is consistent with the expr type.

***Example*** for  Heap tables

```sql
-- Querying the area_name column with a GROUP BY clause
SELECT area_name,MAX(DHQ) FROM area GROUP BY area_no;

[1:8]YAS-04316 not a single-group group function                 
  
-- Assigning the area_name column to the ANY_VALUE function 
SELECT ANY_VALUE(area_name),MAX(DHQ) FROM area GROUP BY area_no;

ANY_VALUE(area_name)                                          max(DHQ)
------------------------------------------------------------- ----------------------------------------------------------------
EastChina                                                          Shanghai
WestChina                                                          Chengdu
SouthChina                                                          Guangzhou
NorthChina                                                          Beijing
CentralChina                                                          Wuhan

```
