```ebnf+diagram
md5::= MD5 "(" expr ")"
```

The MD5 function is used to calculate the MD5 value of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr). The value of expr must be of character type or another type that can be converted to character type. It returns a fixed-length hexadecimal string of varchar(32) type.

This function follows these rules:

- When the value of expr is NULL or an empty string, the function returns NULL.

- When expr is of nchar type or char type of variables, the function will trim the trailing spaces before calculating the MD5 value.

- When expr is of double or float type, the function returns its MD5 value in scientific notation.

- When expr is of bool type, the function returns the MD5 value of its corresponding bool data value (1 and 0).

- expr cannot be BFILE or LOB type data that exceeds 65534 bytes.

- This function does not support vectorization calculation.

***Example*** for Heap tables

```sql

SELECT MD5(1) FROM DUAL;

MD5(1)
---------------------------------
c4ca4238a0b923820dcc509a6f75849b
    
SELECT MD5(NULL) FROM DUAL;

MD5(NULL)
---------

SELECT MD5('') FROM DUAL;

MD5('')
-------

SELECT MD5(' ') FROM DUAL;

MD5('')
---------------------------------
7215ee9c7d9dc229d2921a40e899ec5f
```
