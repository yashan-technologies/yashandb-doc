```ebnf+diagram
rpad::= RPAD "(" expr "," pad_length ["," pad_character] ")"
```

RPAD pads the string represented by [expr](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) with a specified character to a specified length from the right, producing a new string.

**expr**

The value of expr must be of character type or another type that can be converted to character type (LOB and BFILE types support implicit conversion).

- In the vectorized execution engine, expr cannot be LOB type external storage data.

- expr cannot be data of BFILE or LOB types exceeding 65534 bytes.

- When expr is of type NCLOB, NCHAR, or NVARCHAR, the return value is of type NVARCHAR; in other cases, the return value is of type VARCHAR.

- When the value of expr is NULL, the function returns NULL.

**pad_length**

Specifies the final length of the padded string. pad_length is a general expression the same as expr, and must be a numeric type excluding BIT or another type that can be converted to NUMBER, with a value range of [-9223372036854775808, 65534].

- When pad_length is NULL, 0, or negative, the function returns NULL.

- When pad_length is a decimal, the function truncates its decimal part and retains the integer part.

- When pad_length is less than or equal to the length of expr, its effect is equivalent to substring extraction of expr, the function returns a substring of expr truncated to that length from left to right.

- When pad_length is greater than the length of expr, its effect is to pad expr.

**pad_character**

Specifies the content to be padded, which is optional. pad_character is a general expression the same as expr, and must be of character type or another type that can be converted to character type (LOB and BFILE types support implicit conversion).

- If pad_character is omitted, the default padding is space.

- When pad_character is specified, the function reads the characters of pad_character from left to right in a loop and pads them to the left of expr until the length requirement of pad_length is met.

- In the vectorized execution engine, pad_character cannot be LOB type external storage data.

- When the value of pad_character is NULL, the function returns NULL.

  

***Example***

```sql
SELECT RPAD('Shenzhen',16,'NIHAO') AS res FROM DUAL;
RES                  
---------------------
ShenzhenNIHAONIHAONIHA
 
SELECT RPAD('Shenzhen',1,'NIHAO') AS res FROM DUAL;
RES
-----
Shen
 
SELECT RPAD('Shenzhen',5,'NIHAO') AS res FROM DUAL;
RES
-------------
ShenzhenNIH
 
SELECT RPAD('Shenzhen',16) AS res1 FROM DUAL;
RES1
---------------------
Shenzhen  
  
SELECT RPAD('Shenzhen',1.999) AS res1 FROM DUAL;
RES1
-----
Shen
  
SELECT RPAD('Shenzhen','1.999') AS res1 FROM DUAL;
RES1
-----
Shen
```
