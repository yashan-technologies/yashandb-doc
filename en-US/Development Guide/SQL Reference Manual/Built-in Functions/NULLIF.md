```ebnf
nullif = NULLIF "(" expr1 "," expr2 ")".
```

The NULLIF function is used to compare the values of [expr1](../General SQL Syntax/expr) and [expr2](../General SQL Syntax/expr). It returns NULL when they are equal, and returns the value of expr1 when they are not equal.

This function follows these rules:

- Both expr1 and expr2 cannot be of LOB type or JSON type.

- CHAR, VARCHAR can be mixed with NCHAR, NVARCHAR in operations.

- If expr1 and expr2 have different data types and cannot be converted by a specific rule, the function will return a type conversion error.

- If one of expr1 or expr2 is of numeric type and the other is a string type representing a number, the function will return a type conversion error.

- When the computed value of expr1 is NULL, the function returns NULL. When expr1 is a NULL constant, the function will throw an error.

- When expr2 is NULL, the function returns the value of expr1.

The return value type rules of the function are as follows:

- If both expr1 and expr2 are numeric types, the function will determine the data type with the highest precision and return the computed result in that type.

- If both expr1 and expr2 are character types, the return value type will be the type of expr1.

- If expr1 and expr2 are DATE, TIMESTAMP, or timezone types, the return type priority is TIMESTAMP TZ -> TIMESTAMP LTZ -> TIMESTAMP -> DATE.

- In all other scenarios, if expr1 and expr2 have the same type, the function returns a value of that data type.

***Example***

```sql
-- When the values of expr1 and expr2 are not equal
SELECT NULLIF('123','456') res FROM DUAL;
RES
----- 
123

-- When the values of expr1 and expr2 are equal
SELECT NULLIF('123','123') res FROM DUAL;
RES
-----

-- The sales_info table contains the sales quantity and amount information of the organization's product 11001. Now, a record with both quantity and amount as 0 is added.
INSERT INTO sales_info VALUES ('2021','06','0101','11001',0,0,'');
-- When calculating the selling price of product 11001 for each month, a division by zero error occurs.
SELECT year,month,product,amount/quantity 
FROM sales_info
WHERE product='11001';
[1:33]YAS-00011 divided by zero
-- Use the NULLIF function to prevent such errors in advance, reduce exception handling, and avoid interruptions in the application.
SELECT year,month,product,amount/NULLIF(quantity,0) price
FROM sales_info
WHERE product='11001';
YEAR  MONTH PRODUCT         PRICE
----- ----- --------- -----------
2021  10    11001              15
2001  01    11001      16.6666667
2000  12    11001              15
2015  03    11001              15
2021  05    11001              15
2021  06    11001
2015  11    11001              15
```
