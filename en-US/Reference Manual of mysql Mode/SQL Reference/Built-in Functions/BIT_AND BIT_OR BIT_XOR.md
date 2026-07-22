```ebnf
bit_and = BIT_AND "(" [ALL] expr ")" .
bit_or = BIT_OR "(" [ALL] expr ")" .
bit_xor = BIT_XOR "(" [ALL] expr ")" .
```

The BIT_AND/BIT_OR/BIT_XOR aggregate functions perform bitwise calculations on [expr](../General SQL Syntax/expr) using 64-bit BIGINT UNSIGNED data type and return a 64-bit BIGINT UNSIGNED value.

**expr**

A general expression whose value must be of number type or other types that can be converted to number type.

- During bitwise calculations, the default initial value has all bits set to 1. If the input expr is NULL, it will be skipped, and the next expr will be selected for the bitwise calculation.

- When expr is a numeric decimal, it will be rounded before the bitwise calculation. For character-type decimals, the digits after the decimal point will be directly truncated.

- Expr data types of BINARY and BLOB are not allowed.

***Example*** for Standalone Deployment Heap tables

```sql
create table bit_tab(c1 int);
insert into bit_tab values(1);
insert into bit_tab values(0);

select bit_and(c1) from bit_tab ;
return 0;

select bit_or(c1) from bit_tab ;
return 1;

select bit_xor(c1) from bit_tab ;
return 1;

select bit_and(null) from dual;
        bit_and(null) 
--------------------- 
 18446744073709551615

select bit_or(null) from dual;
         bit_or(null) 
--------------------- 
                    0

select bit_xor(null) from dual;
        bit_xor(null) 
--------------------- 
                    0
```
