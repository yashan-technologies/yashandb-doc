```ebnf+diagram
bit_and::= BIT_AND "(" [ALL] expr ")" 
bit_or::= BIT_OR "(" [ALL] expr ")" 
bit_xor::= BIT_XOR "(" [ALL] expr ")" 
```

The BIT_AND/BIT_OR/BIT_XOR aggregate functions perform bitwise calculations on [expr](../../../All Manuals/Reference Manual of mysql Mode/SQL Reference/通用SQL语法/expr) using 64-bit BIGINT UNSIGNED data type and return a 64-bit BIGINT UNSIGNED value.

**expr**

A general expression whose value must be of number type or other types that can be converted to number type.

- During bitwise calculations, the default initial value has all bits set to 1. If the input expr is NULL, it will be skipped, and the next expr will be selected for the bitwise calculation.

- When expr is a numeric decimal, it will be rounded before the bitwise calculation. For character-type decimals, the digits after the decimal point will be directly truncated.

- Expr data types of BINARY and BLOB are not allowed.

***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE bit_tab(c1 INT);
INSERT INTO bit_tab VALUES(1);
INSERT INTO bit_tab VALUES(0);

SELECT BIT_AND(c1) FROM bit_tab ;
return 0;

SELECT bit_or(c1) FROM bit_tab ;
return 1;

SELECT bit_xor(c1) FROM bit_tab ;
return 1;

SELECT BIT_AND(null) FROM dual;
        bit_and(null) 
--------------------- 
 18446744073709551615

SELECT bit_or(null) FROM dual;
         bit_or(null) 
--------------------- 
                    0

SELECT bit_xor(null) FROM dual;
        bit_xor(null) 
--------------------- 
                    0
```
