```ebnf+diagram
array_upper::= ARRAY_UPPER "(" array_var "," dimension ")"
```

The ARRAY_UPPER function calculates the upper limit of the number of elements in the array (array_var) specified by the dimension parameter, returning an INT type value. 

This function does not support vectorization calculation.

**array_var**

An initialized array variable, which cannot be NULL.

**dimension**

The array dimension, that is, the array nesting depth, which is a [general expression](../General SQL Syntax/expr) that must evaluate to an INT type (an error is raised if it exceeds the INT value range). The calculation rules for this value are as follows:

- When dimension is NULL, the function returns NULL.
- The array nesting depth starts from 1; if dimension is less than 1 or greater than the maximum depth, the function returns NULL.

***Example*** for Heap tables

```sql
--Single-layer array
SELECT ARRAY_UPPER(STRING_TO_ARRAY('a,b,c,d,e', ','), 1) LIMIT FROM DUAL;
       LIMIT 
------------ 
  2147483647

SELECT ARRAY_UPPER(STRING_TO_ARRAY('a,b,c,d,e', ','), NULL) LIMIT FROM DUAL;
       LIMIT 
------------ 
  

SELECT ARRAY_UPPER(STRING_TO_ARRAY('a,b,c,d,e', ','), -1) LIMIT FROM DUAL;
       LIMIT 
------------ 
  

--Multi-layer array
SET serveroutput ON
DECLARE
  TYPE arr_type_2 IS VARRAY(10) OF CHAR(5);
  TYPE arr_type_1 IS VARRAY(10) OF arr_type_2;
  b arr_type_1;
BEGIN
    b := arr_type_1(STRING_TO_ARRAY('a1,b1,c1', ',')
                    ,STRING_TO_ARRAY('a2,b2', ',')
                    ,STRING_TO_ARRAY('a3,b3,c3,d3', ','));
    DBMS_OUTPUT.PUT_LINE('Dimension 1 has:'||ARRAY_UPPER(b,1));
    DBMS_OUTPUT.PUT_LINE('Dimension 2 has:'||ARRAY_UPPER(b,2));
END;
/
Dimension 1 has:10
Dimension 2 has:10
```
