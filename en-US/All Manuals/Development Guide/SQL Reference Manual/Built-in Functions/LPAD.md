```ebnf+diagram
lpad::= LPAD "(" expr "," pad_length ["," pad_character] ")"
```

The LPAD function pads the value of [expr](../General SQL Syntax/expr) from the left with a specified character and a specified length, resulting in a new string.

**expr**

The value of expr must be of character type or another type that can be converted to character type.

- In the vectorized execution engine, expr cannot be an out-of-line stored data of LOB type.
- expr cannot be BFILE or LOB type data exceeding 65534 bytes.
- When expr is of type NCLOB, NCHAR, or NVARCHAR, the return value is of NVARCHAR type, while in other scenarios, the return value is of VARCHAR type.
- When the value of expr is NULL, the function returns NULL.

**pad_length**

This specifies the final length of the string after padding. pad_length is a general expression equivalent to expr and must be numeric, or convertible to NUMBER type, with a range of [-9223372036854775808, 65534].

- When the value of pad_length is NULL or in the range of [-9223372036854775808, 0], the function returns NULL.
- When pad_length is a decimal, the function truncates its decimal part, retaining only the integer part.
- If pad_length is less than or equal to the length of expr, it effectively truncates the expr string, returning a substring of expr that is cut from left to right according to that length.
- If pad_length is greater than the length of expr, it effectively pads the expr string.

 **pad_character**

This specifies the content used for padding and is optional. pad_character must be a general expression equivalent to expr and must be of character type or convertible to character type.

- If pad_character is omitted, the default padding character is a space.
- When pad_character is specified, the function reads the characters of pad_character from left to right in a loop and fills them on the left side of expr until the length requirement of pad_length is met.
- In the vectorized execution engine, pad_character cannot be an out-of-line stored data of LOB type.
- When the value of pad_character is NULL, the function returns NULL.

***Example***

```sql
-- The value of pad_length is greater than the length of expr string, normal padding
SELECT LPAD('SHENZHEN NIHAO',20,'NI') AS res FROM DUAL;
RES                                      
-----------------------------------------
NININISHENZHEN NIHAO

-- The value of pad_length is less than/equal to the length of expr string, equivalent to truncating
SELECT LPAD('SHENZHEN NIHAO',1,'NI') AS res FROM DUAL;
RES  
-----
S 

SELECT LPAD('SHENZHEN NIHAO',16) AS res1 FROM DUAL;
RES1
-----------------
  SHENZHEN NIHAO
  
SELECT LPAD('SHENZHEN NIHAO',3.5) AS res1 FROM DUAL;
RES1    
---------
SHE
  
SELECT LPAD('SHENZHEN NIHAO',-3.5) AS res1 FROM DUAL;
RES1
-----
```
