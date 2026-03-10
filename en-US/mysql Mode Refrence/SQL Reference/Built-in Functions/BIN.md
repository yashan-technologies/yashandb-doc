```ebnf+diagram
bin::= BIN "(" expr ")"
```

The BIN function converts a value represented by [expr](../General SQL Syntax/expr) into a BIT binary and returns that BIT value as a VARCHAR type.

**expr**

A general expression whose value must be of RAW, LOB (not exceeding 65534 bytes), numeric types, or other types convertible to NUMBER. The value range is \[-2<sup>63</sup>, 2<sup>63</sup> - 1\].

- When the value of expr is a NUMBER type with decimals (or converts to a NUMBER type), the function directly truncates the integer part.

- When the value of expr is negative, the function returns the corresponding 64-bit two's complement.

- When expr is NULL, the function returns NULL.

***Example*** for  Heap tables

```sql
SELECT BIN('2345.34') res FROM DUAL;
res
--------------
100100101001 
 
SELECT BIN(-235) res FROM DUAL;
res
----------------------------------------------------------------
1111111111111111111111111111111111111111111111111111111100010101

SELECT BIN(b'0100101010') res FROM DUAL;
[1:8]YAS-04401 data type CHAR OR STRING expected, but BIT got
 
```
