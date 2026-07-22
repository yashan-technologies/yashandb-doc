```ebnf
vector_dimension_format = VECTOR_DIMENSION_FORMAT "(" expr ")".
```

The VECTOR_DIMENSION_FORMAT function is used to obtain whether the data type of the vector dimension value is FLOAT32 or FLOAT64.

The return value of this function is of VARCHAR2 type.

**expr**

Indicates the input vector or string-type vector value.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- When a certain expr input is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
-- Input expr as VECTOR type
SELECT VECTOR_DIMENSION_FORMAT(TO_VECTOR('[34.6, 77.8]', 2, FLOAT64) ) FROM dual;

VECTOR_DIMENSION_FORMAT(TO_VECTOR('[34.6,77.8]',2,
--------------------------------------------------
FLOAT64      

```