```ebnf
bin_to_num = BIN_TO_NUM "(" [expr {"," expr}] ")".
```

The BIN_TO_NUM function is used to convert binary to decimal. It converts a binary bit vector string represented by [expr](../General SQL Syntax/expr) combinations into its equivalent numeric value, returning a NUMBER type data.

The BIN_TO_NUM function is applicable only to HEAP tables.

This function supports [0,65535] expr as parameters. It returns 0 directly when there are no input parameters, and returns the conversion result when there are parameters. However, if the calculated value exceeds the range that can be represented by the NUMBER type, an error will occur.

Each expr represents a bit in the vector, and its value can be any numeric type or a character type that can be implicitly converted to NUMBER.

All expr values must be 0/1 or rounded to 0/1; otherwise, an error occurs. The rounding rules are:

- If expr is of FLOAT/DOUBLE type, the function performs round half to even rounding (e.g., 1.5 -> 2, 0.5 -> 0).
- For other types, the function truncates the decimal part (e.g., 1.3 -> 1, 1.6 -> 1).

***Example***

```sql
-- Converts the binary bit vector string 11 to decimal 3
SELECT BIN_TO_NUM(1,1) res FROM DUAL;
RES
-----------
3

-- Converts the binary bit vector string 1101 to decimal 13
SELECT BIN_TO_NUM(b'1',1,0,1) res FROM DUAL;
RES
-----------
13

-- Creates the bt_table_bin_to_num table containing multiple floating-point fields
CREATE TABLE bt_table_bin_to_num(c1 DOUBLE, c2 DOUBLE, c3 DOUBLE, c4 DOUBLE);
INSERT INTO bt_table_bin_to_num VALUES(0.3,0.8,1.3,1.8);

-- For NUMBER data, the function converts to decimal after truncating the decimal part
-- and for floating-point decimals, the function converts to decimal after rounding
SELECT BIN_TO_NUM(0.3) "0.3", BIN_TO_NUM(c1) "d0.3", 
BIN_TO_NUM(0.8) "0.8", BIN_TO_NUM(c2) "d0.8", 
BIN_TO_NUM(1.3) "1.3", BIN_TO_NUM(c3) "d1.3"
FROM bt_table_bin_to_num;
        0.3        d0.3         0.8        d0.8         1.3        d1.3 
----------- ----------- ----------- ----------- ----------- ----------- 
          0           0           0           1           1           1

-- For NUMBER data, the function converts to decimal after truncating the decimal part to 1
SELECT BIN_TO_NUM(1.8) res FROM DUAL;
RES
-----------
1

-- The c4 field is rounded to 2, resulting in an error
SELECT BIN_TO_NUM(c4) res FROM bt_table_bin_to_num;
[1:19]YAS-06001 the value of parameter expression is invalid, the value of the expression must be 0 or 1
```
