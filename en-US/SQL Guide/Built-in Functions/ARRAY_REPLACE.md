```ebnf+diagram
array_replace::= ARRAY_REPLACE "(" array_var "," compare_member "," replace_member ")"
```

The ARRAY_REPLACE function replaces all members in the array (array variable [array_var](../../All Manuals/Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array object](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Types), [array type](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/用户自定义类型) that are equal to compare_member with replace_member, and returns the modified array.

This function adheres to the following rules:

* An error is raised when the member type of array_var or the type of compare_member is a non-scalar type.
* This function can be used as an initialization function for array type variables in PL.
* The result of this function cannot be used as a value in an INSERT statement.
* Replacing array members does not affect the original array variable, only the return value of this function.
* This function does not support vectorization calculation.

**array_var**

The array variable, which can be:

- An existing array, where the array members must be of [basic scalar data types](../../All Manuals/Development Guide/SQL Reference Manual/数据类型/00数据类型).
- NULL, in which case the function returns NULL.

**compare_member**

[General expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose value type must be comparable with the member types of the array_var array. For specifics, please refer to YashanDB's [comparison operation rules](../../All Manuals/Development Guide/SQL Reference Manual/Operators/Comparison Operators).

**replace_member**

[General expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr), whose type must be the same as the array_var members or a data type that can be implicitly converted.

***Example*** for Heap tables

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  arr arr_type := arr_type('a', 'b', 'cc', 'ddd', 'e');
  -- Use ARRAY_REPLACE FUNCTION TO initialize the array
  res arr_type := ARRAY_REPLACE(arr, 'cc', NULL);
BEGIN
  FOR i IN 1 .. res.COUNT LOOP
    IF res(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i ||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i ||': '|| res(i));
    END IF;
  END LOOP;
END;
/
1: a
2: b
3 is NULL
4: ddd
5: e
```
