```ebnf+diagram
array_ndims::= ARRAY_NDIMS "(" array_var ")"
```

The ARRAY_NDIMS function calculates the number of dimensions (nesting depth) of an array ([array variable](../../PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array object](../../PL Reference Manual/PL Objects/User-Defined Types), [array type](../Data Types (yashan Mode)/User-Defined Types)) called array_var, returning an INT type value.

This function does not support vectorization calculation.

**array_var**

An initialized array variable. When array_var is NULL, the function returns NULL.

***Example*** for Heap tables

```sql
SET serveroutput ON

CREATE OR REPLACE TYPE arr_udt_1 IS VARRAY(5) OF INT;
/

CREATE OR REPLACE TYPE arr_udt_2 IS VARRAY(5) OF arr_udt_1;
/

DECLARE
  a arr_udt_1 := arr_udt_1(2);
  b arr_udt_2 := arr_udt_2(a);
  c INT;
BEGIN
  DBMS_OUTPUT.PUT_LINE('Dimension of b: '||ARRAY_NDIMS(b));
  DBMS_OUTPUT.PUT_LINE('Dimension of b(1): '||ARRAY_NDIMS(b(1)));
END;
/
Dimension of b: 2
Dimension of b(1): 1
```
