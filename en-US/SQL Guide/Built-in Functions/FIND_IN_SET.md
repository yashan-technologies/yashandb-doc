```ebnf+diagram
find_in_set::= FIND_IN_SET "(" expr "," strlist ")"
```

The FIND_IN_SET function is used to find the first occurrence of the string represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) in the string list strlist. The function returns a value of INT type.

This function follows these rules:

- In row-wise calculations, the values of expr and strlist cannot be of RAW type. In vectorized calculations, the values of expr and strlist cannot be of time zone type and RAW type.
- Case insensitive.

- Position counting starts from 1. If expr does not exist in strlist, it returns 0.

- The strlist is a string composed of substrings separated by ','. Based on this rule, if expr contains ',', the function cannot guarantee the correctness of the returned result.

**expr, strlist**

- In row-wise calculations, the values of expr and strlist cannot be of RAW type.

- In vectorized calculations, the values of expr and strlist cannot be of boolean type and RAW type.

- In the vectorized execution engine, expr cannot be LOB type out-of-line storage data.

- expr cannot be BFILE, LOB type data exceeding 65534 bytes.

- When either expr or strlist has a NULL value, the function returns NULL.

***Example***

```sql
SELECT FIND_IN_SET('A', 'a,b,c') a,
FIND_IN_SET('Shenzhen', 'Guangdong provice,Shenzhen city') b,
FIND_IN_SET(3, '1,2,3') c,
FIND_IN_SET('Shenzhen city', 'Guangdong,Shenzhen') d
FROM DUAL;

           A            B            C            D
------------ ------------ ------------ ------------
           1            0            3            0
```
