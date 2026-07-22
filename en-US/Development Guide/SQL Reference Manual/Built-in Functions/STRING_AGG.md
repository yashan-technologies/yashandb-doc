```ebnf
string_agg = STRING_AGG "(" [DISTINCT|ALL] string "," separator [order_by_clause] ")".
```

The STRING\_AGG function concatenates multiple rows of data, with a separator between them, returning a single row of CLOB type string. This function has similar functionality to the [GROUP_CONCAT](GROUP_CONCAT) function.

This function does not support vectorization calculation.

**DISTINCT**

Filters out duplicate rows that appear within the same group when calculating the final concatenated result.


DISTINCT does not support LOB data.


**ALL**

The default value, indicating that duplicate rows are not filtered and all rows are concatenated.

**string**

The string can be:

- General expression [expr](../General SQL Syntax/expr)

- A subquery that returns a single row and a single column.

The value of the string must be a character type or another type that can be converted to a character type (LOB types support implicit conversion), but it cannot be of type JSON or time zone types.

If the value of string is NULL, the function returns NULL.

**separator**

Specifies the separator to be used between multiple rows when concatenating them. The separator must be a character constant or a constant expression.

The separator must be specified, but it can be set to NULL.

**order\_by\_clause**

Sorts the strings to be concatenated within the group, with syntax consistent with that described in the SELECT statement.

When a constant number is specified after ORDER BY, it represents the order value of the string.

***Example*** for Heap tables

```sql
-- Create exprs_string_agg table and insert data
CREATE TABLE exprs_string_agg (id INT,name VARCHAR(50),money FLOAT);
INSERT INTO exprs_string_agg 
VALUES  (1,'Dong',10000),(2,'Ming',46450),
        (3,'Hong',46450 ),(4,'Dong',14465),
        (5,'Ming',46450),(6,'Dong',46450);

-- When GROUP BY is not specified, concatenate all rows to get one result
SELECT STRING_AGG(MONEY,',') AS money FROM exprs_string_agg;
MONEY                                                            
---------------------------------------------------------------- 
1.0E+004,4.645E+004,4.645E+004,1.4465E+004,4.645E+004,4.645E+004

-- After grouping, the multiple rows of each group are concatenated into one row, resulting in multi-row results by group
SELECT NAME,STRING_AGG(money,',') AS money FROM exprs_string_agg GROUP BY name;
NAME                       MONEY                                
-------------------------- ------------------------------------ 
Dong                     1.0E+004,1.4465E+004,4.645E+004     
Ming                     4.645E+004,4.645E+004               
Hong                     4.645E+004                          

-- Using ORDER BY clause to sort
SELECT NAME,STRING_AGG(money,',' ORDER BY id DESC) AS money FROM exprs_string_agg GROUP BY name;
NAME                     MONEY                               
------------------------ ------------------------------------
Dong                   4.645E+004,1.4465E+004,1.0E+004       
Ming                   4.645E+004,4.645E+004                 
Hong                   4.645E+004      

-- Using DISTINCT keyword to remove duplicates
SELECT STRING_AGG(DISTINCT name,',') AS names FROM exprs_string_agg;
NAMES
----------------------------------------------------------------
Dong,Ming,Hong
```
