```ebnf
left = LEFT "(" expr "," length ")".
```

The LEFT function returns a substring with capturring the value of [expr](../General SQL Syntax/expr) according to the length specified from the left.

**expr**

The value of expr must be of character type or other types that can be converted to character type.

- In the vectorized execution engine, expr cannot be LOB types for out-of-line storage data.

- expr cannot be BFILE or LOB type data exceeding 65534 bytes.

- When expr is NCLOB/NCHAR/NVARCHAR, the return value is of NVARCHAR type; in other cases, the return value is of VARCHAR type.

- When the value of expr is NULL, the function returns NULL.

**length**

Specifies the length of the string to be extracted. Length is a generic expression that must be a numeric type excluding BIT or other types that can be converted to NUMBER type. The range of values is [-2147483648, 2147483647].

- When the length value is NULL or in the range of [-2147483648, 0], the function returns NULL.  

- When the length value is a decimal, the function will first round it according to the following rules:

  - NUMBER type or floating point: rounded to the nearest integer.  

  - Other types convertible to NUMBER: rounded down (i.e., taking the integer part).
  
- When the length value is greater than the length of the expr string, it is treated as the length of the expr string.

***Example***

```sql
SELECT LEFT(SYSDATE+1,4.99) res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022-          
 
SELECT LEFT(SYSDATE+1,'4.99') res FROM DUAL;
RES                                            
----------------------------------------------------------------
2022
```
