```ebnf
group_concat = GROUP_CONCAT "(" [DISTINCT] string {"," string} [order_by_clause [SEPARATOR sep_character]] ")".
```

The GROUP_CONCAT function adds aggregation functionality to the [CONCAT](CONCAT) function, which means it performs the CONCAT operation on multiple rows within each group for a GROUP BY aggregation. The return value of the function is of CLOB type.

This function does not support vectorization calculation.

**DISTINCT**

Filters out duplicate strings that appear within the same group.


DISTINCT does not support LOB data.


**string**

The value of the string must be a character type or another type that can be converted to a character type, but it cannot be of type JSON or time zone types.

If the string is NULL, the function returns NULL.

The string parameter can be:

- [expr](../General SQL Syntax/expr)
- A single-column subquery that returns a single row

The maximum number of string parameters is 4096.

**order\_by\_clause**

Sorts the strings to be concatenated within the group. Its syntax is consistent with that described in the SELECT statement.

When a constant number is specified after ORDER BY, it represents the order value of the string.

**SEPARATOR sep\_character**

Specifies the separator defined by sep_character to be used between multiple rows when concatenating within the group. The sep_character can only be a character type constant or NULL.

If this statement is not specified, the default is ','.

***Example*** for Heap tables

```sql
-- Create the exprs_group table and insert data
CREATE TABLE exprs_group (expra INT,exprb CHAR(3));
INSERT INTO exprs_group VALUES(1, 'aaa');
INSERT INTO exprs_group VALUES(1, 'bbb');
INSERT INTO exprs_group VALUES(2, 'bbb');
INSERT INTO exprs_group VALUES(2, 'ccc');
INSERT INTO exprs_group VALUES(3, 'ccc');
INSERT INTO exprs_group VALUES(3, 'ddd');
COMMIT;
 
-- Without specifying GROUP BY, concatenate all rows to obtain a single result 
SELECT GROUP_CONCAT(expra,exprb) res FROM exprs_group;
RES                                                             
----------------------------------------------------------------
1aaa,1bbb,2bbb,2ccc,3ccc,3ddd                                  
 
-- After group by, multiple rows of each group are concatenated into a single row, obtaining multi-row results by group
SELECT expra, GROUP_CONCAT(expra,exprb ORDER BY 2 SEPARATOR '$') res
FROM exprs_group
GROUP BY expra;
       EXPRA RES                                                             
------------ ----------------------------------------------------------------
           1 1aaa$1bbb                                                      
           2 2bbb$2ccc                                                      
           3 3ccc$3ddd      
 
-- Subquery
SELECT expra,GROUP_CONCAT((SELECT expra FROM exprs_group WHERE ROWNUM=1),exprb ORDER BY 2 SEPARATOR '$') res
FROM exprs_group
GROUP BY expra;
       EXPRA RES                                                             
------------ ----------------------------------------------------------------
           1 1aaa$1bbb                                                      
           2 1bbb$1ccc                                                      
           3 1ccc$1ddd
```
