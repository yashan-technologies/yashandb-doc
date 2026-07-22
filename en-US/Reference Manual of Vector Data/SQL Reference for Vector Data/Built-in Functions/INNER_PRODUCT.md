```ebnf
inner_product = INNER_PRODUCT "(" expr1 "," expr2 ")".
```

The INNER_PRODUCT function is used to calculate the dot product distance between two vectors, and the function return value is of DOUBLE type.

The INNER_PRODUCT() function has the same meaning as `-1 * VECTOR_DISTANCE(DOT)`.

When using the INNER_PRODUCT function for querying, the query can not be accelerated through the index based on the dot algorithm.


**expr**

Indicates the input vector type data or vector-formatted CHAR/VARCHAR/CLOB type data.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- The dimension data types and dimension counts of the two expr must be consistent.
- When a certain expr input is NULL, the function returns NULL.

