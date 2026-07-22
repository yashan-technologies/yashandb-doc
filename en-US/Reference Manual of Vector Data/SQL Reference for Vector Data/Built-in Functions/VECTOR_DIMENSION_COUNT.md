```ebnf
vector_dimension_count = VECTOR_DIMENSION_COUNT "(" expr ")".
```

The VECTOR_DIMENSION_COUNT function is used to obtain the dimension count of a vector.

The return value of this function is of NUMBER type.

**expr**

Indicates the input vector or string-type vector value.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- When a certain expr input is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT VECTOR_DIMENSION_COUNT('[7.03,3.55,-2.93,8.96]') AS col FROM dual;

        COL
-----------
          4

```