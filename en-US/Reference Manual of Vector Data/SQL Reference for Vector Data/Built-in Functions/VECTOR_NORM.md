```ebnf
vector_norm = VECTOR_NORM "(" expr ")".
```

The VECTOR_NORM function is used to calculate the Euclidean norm of a vector, also known as the L2 norm. The L2 norm is a commonly used vector norm for measuring the length or magnitude of a vector. It is calculated by taking the square root of the sum of the squares of all elements in the vector.

The return value of this function is of BINARY_DOUBLE type.

**expr**

Indicates the input vector or string-type vector value.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- When a certain expr input is NULL, the function returns NULL.

***Example*** for Standalone Deployment Heap tables

```sql
-- Input expr as VECTOR type

SELECT VECTOR_NORM(TO_VECTOR('[34.6, 77.8]', 2, FLOAT64)) FROM dual;

VECTOR_NORM(TO_VECTOR('[34.6,77.8]',2,FLOAT64))
-----------------------------------------------
8.515E+001
```