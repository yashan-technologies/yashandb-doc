```ebnf+diagram
hextoraw::= HEXTORAW "(" expr ")"
```

The HEXTORAW function converts the hexadecimal string represented by [expr](../General SQL Syntax/expr) into binary data, returning a result of RAW type.

The function converts every two hexadecimal characters in expr into one byte, for example, the string 'ab' is converted to '0xab'; if the number of characters in expr is odd, the function first pads with four consecutive zero bits, turning the string 'abc' into '0x0abc'.

This function does not support vectorization calculation.

The value of expr must be of character type or another type that can be converted to character type, but it cannot be any LOB type other than BLOB. When the value of expr is NULL, the function returns NULL.

If expr contains characters that are not in the hexadecimal representation range (0-9, a-f, A-F), the function reports an error.

When this function is used to insert or update a RAW column field, if the byte length of expr exceeds twice the width of the RAW column, the function reports an error.

***Example*** for Heap tables

```sql
SELECT HEXTORAW('1234abcd') res FROM DUAL;
RES
-------------------- 
1234ABCD

SELECT HEXTORAW('') res FROM DUAL;
RES
-------------- 
              
-- Reports an error if it contains characters that are not in the hexadecimal representation range (0-9, a-f, A-F)
SELECT HEXTORAW('1234abcddfdfh') res FROM DUAL;
YAS-00223 invalid hex number

-- Pads with 0 in front for odd length
SELECT HEXTORAW('1abcdef') res FROM DUAL;
RES
--------
01ABCDEF

-- Reports an error if it exceeds twice the width of the RAW column
CREATE TABLE tb_raw(c1 RAW(4));
INSERT INTO tb_raw VALUES('abcdabcd');
INSERT INTO tb_raw VALUES('abcdabcda');
YAS-04008 C1 size exceeding limit 4
```
