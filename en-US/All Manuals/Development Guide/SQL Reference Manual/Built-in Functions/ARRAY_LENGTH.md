```ebnf+diagram
array_length::= ARRAY_LENGTH "(" array_var "," dimension ")"
```

The ARRAY_LENGTH function calculates the length of the array (i.e., the number of members) for the array variable array_var at the dimension specified. It returns a value of INT type.

This function does not support vectorization calculation.

**array_var**

An initialized array variable, which cannot be NULL.

**dimension**

The array dimension, which is the depth of array nesting, is a [general expression](../General SQL Syntax/expr) whose value type must be INT (an error occurs if it exceeds the INT value range).

- When dimension is NULL, the function returns NULL.
- The array nesting depth starts from 1, and when dimension is less than 1 or greater than the maximum depth, the function returns NULL.
- When multiple arrays exist at the same dimension, the function returns the length of the longest array.

***Example*** for Heap tables

```sql
-- Single-layer array
SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), 1) len FROM DUAL;
         LEN 
------------ 
           5

SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), NULL) len FROM DUAL;
         LEN 
------------ 

SELECT ARRAY_LENGTH(STRING_TO_ARRAY('a,b,c,d,e', ','), -1) len FROM DUAL;
         LEN 
------------ 

-- Multi-layer array
SET serveroutput ON
DECLARE
  TYPE arr_type_2 IS VARRAY(10) OF CHAR(5);
  TYPE arr_type_1 IS VARRAY(10) OF arr_type_2;
  b arr_type_1;
BEGIN
    b := arr_type_1(STRING_TO_ARRAY('a1,b1,c1', ',')
                    ,STRING_TO_ARRAY('a2,b2', ',')
                    ,STRING_TO_ARRAY('a3,b3,c3,d3', ','));
    DBMS_OUTPUT.PUT_LINE('Dimension 1 has:'||ARRAY_LENGTH(b,1));
    DBMS_OUTPUT.PUT_LINE('Dimension 2 has:'||ARRAY_LENGTH(b,2));
END;
/
Dimension 1 has:3
Dimension 2 has:4
```
