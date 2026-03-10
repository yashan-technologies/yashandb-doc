```ebnf+diagram
nullif::= NULLIF "(" expr1 "," expr2 ")"
```

The NULLIF function is used to compare the values of [expr1](../General SQL Syntax/expr) and [expr2](../General SQL Syntax/expr). It returns NULL when they are equal, and returns the value of expr1 when they are not equal.

This function follows the rules below:

- Neither expr1 nor expr2 can be of BLOB type or TEXT type.

- CHAR, VARCHAR can be mixed with NCHAR, NVARCHAR in operations.

- If expr1 and expr2 have different data types and cannot be converted according to certain rules, the function will return a type conversion error.

- If one of expr1 or expr2 is a numeric type, and the other is a string type of a number, the function will return a type conversion error.

- When the calculated result of expr1 is NULL, the function returns NULL. If expr1 is a NULL constant, the function will raise an error.

- When expr2 is NULL, the function returns the value of expr1.

The rules for the return value type of the function are as follows:

- When both expr1 and expr2 are numeric types, the function will determine the data type with the highest precision among them and return the result converted to that type.

- When both expr1 and expr2 are character types, the function returns the type of expr1.

- When one of expr1 or expr2 is of DATE type and the other is of TIMESTAMP type, the function returns TIMESTAMP type.

- In all other cases, when expr1 and expr2 have the same type, the function returns a value of that data type.

***Example*** for  Heap tables

```sql
-- When the values of expr1 and expr2 are not equal
SELECT NULLIF('123','456') res FROM DUAL;
res
----- 
123

-- When the values of expr1 and expr2 are equal
SELECT NULLIF('123','123') res FROM DUAL;
res
-----

-- The sales_info1 table contains the sales quantity and amount information for the institution's 11001 product, with one record where both quantity and amount are 0
CREATE TABLE sales_info1
(year CHAR(4) NOT NULL,
 month CHAR(2) NOT NULL,
 branch CHAR(4) NOT NULL,
 product CHAR(5) NOT NULL,
 quantity NUMBER DEFAULT 0 NOT NULL,
 amount NUMBER(10,2) DEFAULT 0 NOT NULL,
 salsperson CHAR(10) NOT NULL)
PARTITION BY HASH(branch);
INSERT INTO sales_info1 VALUES ('2001','01','0201','11001',30,500,'Anna');
INSERT INTO sales_info1 VALUES ('2021','06','0101','11001',0,0,'Mask');

-- When calculating the selling price of product 11001 for each month, a division by zero error will occur
SELECT year,month,product,amount/quantity 
FROM sales_info1
WHERE product='11001';
[1:33]YAS-00011 divided by zero

-- Using the NULLIF function to prevent such errors in advance, reducing exception handling, and avoiding interruptions in the application
SELECT year,month,product,amount/NULLIF(quantity,0) price
FROM sales_info1
WHERE product='11001';
year  month product         price
----- ----- --------- -----------
2021  06    11001
2015  01    11001      16.6666667
```
