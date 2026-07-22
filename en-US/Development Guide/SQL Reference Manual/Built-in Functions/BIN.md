```ebnf
bin = BIN "(" expr ")".
```

The BIN function converts a data represented by [expr](../General SQL Syntax/expr) into a BIT binary and returns that BIT value as a VARCHAR type.

**expr**

A general expression, its value must be of RAW, LOB (not exceeding 65534 bytes), numeric type, or other types that can be converted to NUMBER type, with a value range of \[-2<sup>63</sup>, 2<sup>63</sup> - 1\].

- In the vectorized execution engine, expr cannot be an external storage data of LOB type.

- When the value of expr is a NUMBER type with decimals (or converted to NUMBER type), the function will directly truncate the integer part.

- When the value of expr is negative, the function returns the corresponding 64-bit two's complement.

- When expr is NULL, the function returns NULL.

***Example***

```sql
SELECT BIN('2345.34') res FROM DUAL;
RES
--------------
100100101001 
 
SELECT BIN(b'0100101010') res FROM DUAL;
RES
------------------
100101010    
 
SELECT BIN(-235) res FROM DUAL;
RES
----------------------------------------------------------------
1111111111111111111111111111111111111111111111111111111100010101
```
