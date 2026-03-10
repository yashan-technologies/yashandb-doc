```ebnf+diagram
dump::= DUMP "(" expr ["," return_fmt ["," start_position ["," length ]]] ")"
```
The DUMP function returns a VARCHAR type value, which includes the data type code of the expression, byte length, and internal representation. The returned result uses the database's character set.

This function does not support vectorization calculation.

**expr**

- expr is the expression that needs to be displayed.
    - expr cannot be of type CLOB or BFILE.
    - When expr is NULL, the function returns a 4-byte 'NULL'.

**return_fmt**

- Used to specify the format of the return value, with the following optional values:

    - 8: Return the result in octal format.
    - 10: Return the result in decimal format (default).
    - 16: Return the result in hexadecimal format.
    - 17: Attempts to interpret each byte as a printable character (based on the character set, typically ASCII).
        - If the byte is printable (value range [32,127]), the character is displayed directly.
        - If the byte is not printable, it falls back to hexadecimal representation.

- Character set information: By default, the return value does not include character set information. If character set information is needed, 1000 can be added to the above format values. For example:
    -  1008: Return the result in octal and include the character set name.

**start_position**

Used to specify the starting position of the internal representation to return.

**length**

Used to specify the length of the internal representation to return.

***Example*** for Heap tables

```sql
SELECT DUMP('abc', 1017) A FROM DUAL;

A                                             
--------------------------------------------- 
Typ=24 Len=3 CharacterSet=AL32UTF8: a,b,c   

SELECT DUMP('————', 1010, 4, 10) A FROM dual;

A                                     
---------------------------------------------------------------- 
Typ=24 Len=12 CharacterSet=AL32UTF8: 226,128,148,226,128,148,226,128,148
```
