```ebnf+diagram
bitand::= BITAND "(" expr1 "," expr2 ")"

bitor::= BITOR "(" expr1 "," expr2 ")"

bitxor::= BITXOR "(" expr1 "," expr2 ")"
```

The BITAND/BITOR/BITXOR functions perform bitwise AND/OR/XOR operations on two data values, resulting in bitwise 0s or 1s, and converting multiple 0s or 1s from binary to a decimal value for return.

The values of [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)1 and [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr)2 can be of types TINYINT, SMALLINT, INT, BIGINT, NUMBER, TIME, or character data that can be converted to NUMBER type (a conversion failure will return a type conversion error). For other types, a type not supported error will be returned.

The function first converts expr1 and expr2 to binary, then performs bitwise operations. The conversion rules and constraints are the same as those of the [BIN](BIN) function.

If either expr1 or expr2 has a value of NULL, the function returns NULL.

  

***Example***

```sql
SELECT BITAND('',1) b1,
BITAND('2.35',3) b2,
BITOR(5,-5) b3,
BITXOR(3456,1.99) b4
FROM DUAL;
                   B1                    B2                    B3                    B4
--------------------- --------------------- --------------------- ---------------------
                                          2                    -1                  3457

SELECT BITAND(TIME('00:00:00'),1) FROM dual;

BITAND(TIME('00:00:00'),1)
--------------------------
                         0

```
