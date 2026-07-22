```ebnf
rawtohex = RAWTOHEX "(" expr ")".
```

The RAWTOHEX function is used to convert the binary data expressed by [expr](../General SQL Syntax/expr) into a hexadecimal string, returning a result of VARCHAR type.

This function follows the rules below:

- The function converts each byte in expr into two hexadecimal character substrings.

- The function does not support vectorization calculation.

- The functionality of the function differs when used as a PL built-in function.

**expr**

A general expression that cannot be of type LOB, JSON, or UDT, etc.

- When the data type of expr is not RAW, the function will first convert expr (which is represented by a certain number of data bytes) into RAW type data with the same number of data bytes (the numerical value is not modified).

- When the value of expr is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SELECT RAWTOHEX(5) res FROM DUAL;
RES
-------------------- 
05

SELECT RAWTOHEX('') res FROM DUAL;
RES
-------------- 
              
SELECT RAWTOHEX('1234abcddfdfh') res FROM DUAL;
RES
---------------------------------------------------------
31323334616263646466646668

SELECT RAWTOHEX(CAST('aa' as clob)) res FROM DUAL;
[1:17]YAS-04401 data type RAW expected, but CLOB got

SELECT RAWTOHEX(CASt('aa' as raw(10))) res from DUAL;
RES
---------
AA
```
