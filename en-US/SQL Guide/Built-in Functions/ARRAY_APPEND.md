```ebnf+diagram
array_append::= ARRAY_APPEND "(" array_var "," new_member ")"
```

The ARRAY_APPEND function adds new_member to the end of the array (see [array variable](../../All Manuals/Development Guide/PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array object](../../All Manuals/Development Guide/PL Reference Manual/PL Objects/User-Defined Types), [array type](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/User-Defined Types)) array_var and returns the resulting array after the addition.

This function follows these rules:

* It can be used as a special array initialization function in PL.
* The function result cannot be used as a value in an INSERT statement.
* This function cannot be used on arrays containing non-scalar type members.
* After executing this function, the number of members in array_var does not change.
* This function does not support vectorization calculation.

**array_var**

An array variable, which can have the following values:

- An existing array, with members that must be of [ordinary scalar data types](../../All Manuals/Development Guide/SQL Reference Manual/Data Types (yashan Mode)/00Data Types (yashan Mode)).
- NULL, in which case a new array will be created with the member type of new_member (new_member cannot be NULL), and the new array will contain only the single member new_member.

**new_member**

A [general expression](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/expr) whose type must be the same as the members of the array_var or a data type that can be implicitly converted to it.

When array_var is not NULL, new_member can be NULL, indicating the addition of a NULL member to the end of the array array_var.

***Example*** for Heap tables

```sql
SET serveroutput ON

DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  a arr_type := arr_type('a', 'b', 'c');
  b arr_type;
BEGIN
  -- ADD a member 'd' TO the END OF a, generate a TEMPORARY array AND assign it TO b
  b := ARRAY_APPEND(a, 'd');
  FOR i IN 1 .. b.COUNT LOOP
    IF b(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(b(i));
    END IF;
  END LOOP;
END;
/
a
b
c
d

-- A new array is generated when the first parameter is NULL
DECLARE
  TYPE arr_type IS VARRAY(10) OF CHAR(5);
  b arr_type;
BEGIN
  -- Generate a NEW array  
  b := ARRAY_APPEND(NULL, 'abcd');
  FOR i IN 1 .. b.COUNT LOOP
    IF b(i) IS NULL THEN
	  DBMS_OUTPUT.PUT_LINE(i||' is NULL');
    ELSE
      DBMS_OUTPUT.PUT_LINE(i||' is: '||b(i));
    END IF;
  END LOOP;
END;
/
1 is: abcd
```
