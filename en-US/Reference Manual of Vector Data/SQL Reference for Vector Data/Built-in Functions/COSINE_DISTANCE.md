```ebnf
cosine_distance = COSINE_DISTANCE "(" expr1 "," expr2 ")".
```

The COSINE_DISTANCE function is used to calculate the cosine distance between two vectors, and the function return value is of DOUBLE type.

The COSINE_DISTANCE function is a simplified usage of the VECTOR_DISTANCE function when the **metric** is configured as `COSINE`.

When using the COSINE_DISTANCE function for querying, if an index based on the cosine algorithm has been created on the corresponding column, the query can be accelerated through the index.


**expr**

Indicates the input vector type data or vector-formatted CHAR/VARCHAR/CLOB type data.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- The dimension data types and dimension counts of the two expr must be consistent.
- When a certain expr input is NULL, the function returns NULL.


***Example*** for Standalone Deployment Heap tables

```sql
CREATE TABLE table_cosine_test (c_VECTOR VECTOR(4),c_VECTOR_32 VECTOR(4,float32),c_VECTOR_64 VECTOR(4,float64),c_char char(50),c_varchar varchar(50),c_varchar_32000 varchar(32000),c_varchar_65534 varchar(65534),c_clob clob);
INSERT INTO table_cosine_test VALUES('[2.33,6.10,0.77,5.06]','[5.66,3.51,8.81,6.53]','[1.50,-7.12,2.89,3.10]','[6.30,-5.90,-7.90,8.79]','[-7.44,6.63,-8.20,-4.35]','[7.09,3.85,-5.07,8.33]','[9.89,-9.27,-8.74,-6.31]','[1.32,4.86,6.29,9.10]');
COMMIT;

SELECT COSINE_DISTANCE(c_VECTOR,c_VECTOR) AS col FROM table_cosine_test;

        COL
-----------
          0

SELECT COSINE_DISTANCE(c_VECTOR,c_VECTOR_32) AS col FROM table_cosine_test;

        COL
-----------
 3.008E-001

SELECT COSINE_DISTANCE(c_VECTOR,c_VECTOR_64) AS col FROM table_cosine_test;

[1:8]YAS-00906 requires all input VECTORs to have the same dimension format : encountered dimension formats (FLOAT32, FLOAT64)

SELECT COSINE_DISTANCE(c_VECTOR,c_char) AS col FROM table_cosine_test;

        COL
-----------
 8.593E-001

SELECT COSINE_DISTANCE(c_VECTOR,c_varchar) AS col FROM table_cosine_test;

        COL
-----------
 1.046E+000

SELECT COSINE_DISTANCE(c_VECTOR,c_varchar_32000) AS col FROM table_cosine_test;

        COL
-----------
 2.548E-001

SELECT COSINE_DISTANCE(c_VECTOR,c_varchar_65534) AS col FROM table_cosine_test;

        COL
-----------
 1.502E+000
```
