```ebnf
l2_distance = L2_DISTANCE "(" expr1 "," expr2 ")".
```

The L2_DISTANCE function is used to calculate the Euclidean distance between two vectors, and the function return value is of DOUBLE type.

The L2_DISTANCE function is a simplified usage of the VECTOR_DISTANCE function when the **metric** is configured as `EUCLIDEAN`.

When querying with the L2_DISTANCE function, if an index based on the Euclidean distance algorithm or the squared Euclidean distance algorithm is created on the corresponding column, query performance can be accelerated via the index.


**expr**

Indicates the input vector type data or vector-formatted CHAR/VARCHAR/CLOB type data.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- The dimension data types and dimension counts of the two expr must be consistent.
- When a certain expr input is NULL, the function returns NULL.

