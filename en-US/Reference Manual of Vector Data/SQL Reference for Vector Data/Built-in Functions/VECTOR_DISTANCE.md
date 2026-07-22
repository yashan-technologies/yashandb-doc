```ebnf
vector_distance = VECTOR_DISTANCE "(" expr1 "," expr2 [ "," metric ] ")".

l2_distance = L2_DISTANCE "(" expr1 "," expr2 ")".

inner_product = INNER_PRODUCT "(" expr1 "," expr2 ")".

cosine_distance = COSINE_DISTANCE "(" expr1 "," expr2 ")".
```

The VECTOR_DISTANCE function is used to calculate the distance between two vectors, and the function return value is of DOUBLE type.

When using the VECTOR_DISTANCE function for querying, the query can be accelerated through the index only if an index has been created on the corresponding column and the vector distance calculation method is the same.


**expr**

Indicates the input vector type data or vector-formatted CHAR/VARCHAR/CLOB type data.

- When inputting vector data of CHAR/VARCHAR/CLOB types, it must be a non-empty array enclosed within `[]`, and the the value can not be nan and inf.
- The dimension data types and dimension counts of the two expr must be consistent.
- When a certain expr input is NULL, the function returns NULL.


**metric**

Supports the following five vector distance calculation methods. When omitted, it indicates COSINE.

- EUCLIDEAN: Indicates the Euclidean distance between two vectors.
- EUCLIDEAN_SQUARED: Indicates the Euclidean distance between two vectors without taking the square root.
- L2_SQUARED: Same as EUCLIDEAN_SQUARED.
- COSINE: Indicates the cosine distance between two vectors, which is a synonym for the independent function COSINE_DISTANCE.
- DOT: Indicates the negative inner product distance between two vectors.

When the metric is not configured, the distance calculation method to be used is determined based on the vector index situation on the input vector columns.

- If neither expression (expr) has a vector index, the COSINE distance algorithm is used;
- If only one expression has a vector index, the distance algorithm of that vector index is used;
- If both expressions have vector indexes and the distance algorithm of the indexes are consistent, that distance algorithm is used;
- If both expressions have vector indexes but the distance algorithm of the indexes are inconsistent, the COSINE distance algorithm is used;
- If the input expression is a subquery, it is considered that the expression does not have an index and the distance algorithm follows the judgment rules described above..

***Example*** for Standalone Deployment Heap tables

```sql
-- Input expr as CHAR type

CREATE TABLE chtb(a char(10));
INSERT INTO chtb values('[1,1,1]');
SELECT VECTOR_DISTANCE(chtb.a, '[1,1,1]') FROM chtb;

VECTOR_DISTANCE(CHTB.A,'[1,1,1]')
---------------------------------
​                               0

-- Input expr as VARCHAR type

CREATE TABLE vctb(a varchar(10));
INSERT INTO vctb values('[1,1,1]');

SELECT VECTOR_DISTANCE(vctb.a, '[1,1,1]') FROM vctb;
VECTOR_DISTANCE(VCTB.A,'[1,1,1]')
---------------------------------
​                0

-- Input expr as CLOB type

CREATE TABLE ctb(a clob);
INSERT INTO ctb values('[1,1,1]');
SELECT VECTOR_DISTANCE(ctb.a, '[1,1,1]') FROM ctb;
VECTOR_DISTANCE(CTB.A,'[1,1,1]')
--------------------------------
​                0

-- If the data types of the input parameter vector values are inconsistent, an execution error will occur.

CREATE TABLE vt(a vector(3, FLOAT32));
CREATE TABLE vt2(a vector(3, FLOAT64));
INSERT INTO vt values('[1,1,1]');
INSERT INTO vt2 values('[2,2,2]');

SELECT l2_distance(vt.a, vt2.a) FROM vt, vt2;

[1:8]YAS-00906 requires all input vectors to have the same dimension format : encountered dimension formats (FLOAT32, FLOAT64)

-- If the dimensions of the input parameter vector values are inconsistent, an execution error will occur.

SELECT l2_distance('[1,1,1]', '[2,2]') FROM dual;

[1:8]YAS-00905 vector dimension count mismatch (3, 2)
```
