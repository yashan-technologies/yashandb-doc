```ebnf+diagram
array_remove::= ARRAY_REMOVE "(" array_var "," compare_member ")"
```

The ARRAY_REMOVE function removes all members from the array ([array variable](../../All Manuals/Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array object](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Types), [array type](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/User-Defined Types)) array_var that are the same as compare_member and returns the array after deletion.

This function follows these rules:

* In PL, it can be used as an initialization function for array-type variables.
* The function result cannot be used as a value in an INSERT statement.
* An error is raised if the member type of array_var or the type of compare_member is a non-scalar type.
* Deleting array members does not affect the original array variable; it only affects the return value of the function.
* This function does not support vectorization calculation.

**array_var**

An array variable, which can be:

- An existing array where the members must be of [ordinary scalar data types](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/00Data Types (yashan Mode)).
- NULL, in which case the function returns NULL.

**compare_member**

A [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) whose value type must be comparable to the type of array_var's array members. For specific rules, refer to YashanDB's [comparison operation rules](../../All Manuals/Development Guide/SQL Reference Manual/Operators/Comparison Operators).

***Example*** for Heap tables

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  arr  arr_type := arr_type('123', '234', '333', '234', '23');
  -- Use ARRAY_REMOVE FUNCTION TO initialize the array
  res  arr_type := ARRAY_REMOVE(arr, 234);
BEGIN
  FOR i IN 1 .. arr.COUNT LOOP
    DBMS_OUTPUT.PUT_LINE('arr'||i||': '||arr(i));
  END LOOP;
  DBMS_OUTPUT.PUT_LINE('---------------');
  FOR i IN 1 .. res.COUNT LOOP
    DBMS_OUTPUT.PUT_LINE('res'||i||': '||res(i));
  END LOOP;
END;
/
arr1: 123
arr2: 234
arr3: 333
arr4: 234
arr5: 23
---------------
res1: 123
res2: 333
res3: 23
```
