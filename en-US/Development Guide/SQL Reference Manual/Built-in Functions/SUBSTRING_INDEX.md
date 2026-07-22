```ebnf
substring_index = SUBSTRING_INDEX "(" expr "," delim "," count ")".
```

The SUBSTRING_INDEX function is used to extract the substring from the source string [expr](../General SQL Syntax/expr) before the delim separator appears for the count-th time.

**expr**

A general expression whose value must be of character type or other types that can be converted to character type, excluding JSON and LOB types.

- When expr is of NCHAR/NVARCHAR type, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

**delim**

The separator used to split the source string expr. delim is a general expression that is the same as expr, and must be character-type data or other types that can be converted to character type, excluding JSON and LOB types.

- If a non-character type representation is used for inputting decimals between `(0,1)`, the converted delim will remove the leading 0 before the decimal point for matching.

- If delim is not found in expr, the function will return the entire content of expr.

- If the value of delim is NULL, the function returns NULL.

**count**

Indicates the number of times the separator appeared, used to locate the termination position of the split. count is a general expression that is the same as expr, and must be a numeric data type that is not BIT or other types that can be converted to NUMBER, with a value range of [-2147483648,2147483647]. A positive value indicates to return all content to the left of the count-th occurrence of the separator (counting from the left), while a negative value indicates to return all content to the right of the count-th occurrence of the separator (counting from the right).

- When the value of count is a NUMBER type with decimals, the function will round it to the nearest integer.

- When the value of count is a FLOAT type, the function will round it using odd-even rounding.

- When the value of count exceeds the number of occurrences of delim in expr, the function will return the entire content of expr.

- When the value of count is NULL or 0, the function returns NULL.

***Example***

```sql
SELECT SUBSTRING_INDEX('192.168.0.1',0.1, 1) a
,SUBSTRING_INDEX('192.168.0.1','.', 2) b
,SUBSTRING_INDEX('192.168.0.1','.', 3) c
,SUBSTRING_INDEX('192.168.0.1','.', 4) d
FROM DUAL;
A     B         C             D             
----- --------- ------------- ------------- 
192   192.168   192.168.0     192.168.0.1  

SELECT SUBSTRING_INDEX('192.168.0.1','.',-1) a
,SUBSTRING_INDEX('192.168.0.1','.',-2) b
,SUBSTRING_INDEX('192.168.0.1','.',-3) c
,SUBSTRING_INDEX('192.168.0.1','.',-4) d
FROM DUAL;
A     B     C         D             
----- ----- --------- ------------- 
1     0.1   168.0.1   192.168.0.1  

SELECT SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 as NUMBER)) a
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 as NUMBER)) b
,SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 AS FLOAT)) c
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 AS FLOAT)) d
,SUBSTRING_INDEX('192.168.0.1','.',CAST(1.5 AS DOUBLE)) e
,SUBSTRING_INDEX('192.168.0.1','.',CAST(2.5 AS DOUBLE)) f
FROM DUAL;
A         B             C         D         E         F         
--------- ------------- --------- --------- --------- --------- 
192.168   192.168.0     192.168   192.168   192.168   192.168  

SELECT SUBSTRING_INDEX(NULL,'.',1) a
,SUBSTRING_INDEX('192.168.0.1',NULL,1) b
,SUBSTRING_INDEX('192.168.0.1','.',NULL) c
,SUBSTRING_INDEX('192.168.0.1','.',0) d
FROM DUAL;
A     B     C     D     
----- ----- ----- ----- 
```
