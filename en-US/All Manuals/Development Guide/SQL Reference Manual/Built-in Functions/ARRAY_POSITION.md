```ebnf+diagram
array_position::= ARRAY_POSITION "(" array_var "," compare_member [ "," start_locate ] ")"
```

The ARRAY_POSITION function searches for the first occurrence of compare_member in the array ([array variable](../../PL Reference Manual/PL Language Fundamentals/Variables/Collection Variables), [array object](../../PL Reference Manual/PL Objects/User-Defined Types), [array type](../Data Types/User-Defined Types) array_var, starting from start_locate, and returns the position as an INT type.

This function does not support vectorization calculation.

**array_var**

An array variable, which can be:

- An existing array, where the array members must be [ordinary scalar data types](../Data Types/00Data Types).
- NULL, in which case the function returns NULL.

**compare_member**

[A general expression](../General SQL Syntax/expr), whose value type must be comparable with the type of array_var array members, refer to YashanDB's [comparison operation rules](../Operators/Comparison Operators) for details.

**start_locate**

[A general expression](../General SQL Syntax/expr), which cannot be NULL, and the value type must be INT or implicitly convertible to INT.

If start_locate is negative or exceeds the length of the array, the function returns NULL.

***Example*** for Heap tables

```sql
CREATE OR REPLACE TYPE arr_position_type IS VARRAY(10) OF CHAR(5);
/

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd'), 'c') pos FROM DUAL;
         POS 
------------ 
           3

SELECT ARRAY_POSITION(arr_position_type('a', 'b', NULL, 'd', 'c'), NULL) pos FROM DUAL;
         POS 
------------ 
           3

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd', 'c'), 'c', 4) pos FROM DUAL;
         POS 
------------ 
           5

SELECT ARRAY_POSITION(arr_position_type('a', 'b', 'c', 'd', 'c'), 1, 4) pos FROM DUAL;
         POS 
------------ 
           
```
