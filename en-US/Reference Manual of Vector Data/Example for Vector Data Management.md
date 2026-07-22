This chapter describes the steps for creating users, granting roles, creating vector tables, inserting vector data, creating vector indexes, and common vector queries.

## Create Users and Grant Roles

After the default installation, YashanDB comes with a preconfigured super administrator user `sys`. You need to connect to the database with the `sys` user to create a database test user for this trial (using vector_user/vector_user as an example) and grant permissions.

```bash
-- Connect to the database as sys user
yasql sys/********@192.168.1.2:1688
```

```sql
-- Create vector database user
CREATE USER vector_user IDENTIFIED BY vector_user;

-- Grant connect permission
GRANT CONNECT TO vector_user;

-- Grant resource permission
GRANT RESOURCE TO vector_user;

-- Grant select_catalog_role permission
GRANT SELECT_CATALOG_ROLE TO vector_user;

-- Grant DBA permission (optional)
GRANT DBA TO vector_user;
```

## Create Vector Table

Connect to the database as the vector_user user and create a table for storing vector data. YashanDB supports creating HEAP-organized vector tables. Vector columns must use the VECTOR type and specify the dimension.

```bash
-- Connect to the database as vector_user
yasql vector_user/********@192.168.1.2:1688
```

```sql
-- Create vector table
CREATE TABLE vector_table(
    doc_id BIGINT NOT NULL,
    chunk_id BIGINT NOT NULL,
    content VARCHAR(4000),
    embedding VECTOR(10, FLOAT32)
) ORGANIZATION HEAP;

-- View table structure
DESC vector_table;

-- Confirm table creation
SELECT table_name FROM user_tables WHERE table_name = 'VECTOR_TABLE';
```

## Insert Vector Data

Insert business data and vector data into the vector table. Vector data can be converted from strings using the TO_VECTOR function.

```sql
-- Insert single record
INSERT INTO vector_table VALUES(
    1,
    1,
    'This is the first document about machine learning.',
    TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32)
);

-- Insert multiple records
INSERT INTO vector_table VALUES
(2, 1, 'Deep learning is a subset of machine learning.', TO_VECTOR('[0.5, 0.6, 0.7, 0.8, 0.9, 1.0, 1.1, 1.2, 1.3, 1.4]', 10, FLOAT32)),
(3, 1, 'Natural language processing deals with text data.', TO_VECTOR('[1.0, 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 1.8, 1.9]', 10, FLOAT32)),
(4, 1, 'Computer vision enables machines to see and understand images.', TO_VECTOR('[1.5, 1.6, 1.7, 1.8, 1.9, 2.0, 2.1, 2.2, 2.3, 2.4]', 10, FLOAT32)),
(5, 1, 'Reinforcement learning is about agents learning from environment.', TO_VECTOR('[2.0, 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7, 2.8, 2.9]', 10, FLOAT32));

-- Commit transaction
COMMIT;

-- View inserted data
SELECT doc_id, chunk_id, content FROM vector_table;
```

## Create Vector Index

Create an HNSW vector index on the vector column to accelerate approximate vector queries.

By default, HNSW indexes are built in memory. The HNSW_BUILD_MEM_SIZE parameter specifies the memory available for a single build task. If the required memory exceeds the configured limit or there is insufficient memory, the HNSW index will use a tablespace-based storage build method.

```sql
-- View the current value of the HNSW_BUILD_MEM_SIZE parameter
SHOW PARAMETER HNSW_BUILD_MEM_SIZE;
NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
HNSW_BUILD_MEM_SIZE                                              64M

-- Temporarily adjust the HNSW_BUILD_MEM_SIZE parameter value as needed
ALTER SYSTEM SET HNSW_BUILD_MEM_SIZE = 256M;

-- Create HNSW vector index
CREATE VECTOR INDEX hnsw_index
  ON vector_table(embedding)
  ORGANIZATION NEIGHBOR GRAPH
  WITH DISTANCE COSINE PARAMETERS(
    TYPE HNSW,
    M 16,
    EFCONSTRUCTION 64
  );

-- View index creation status
SELECT index_name, index_type, status FROM user_indexes WHERE index_name = 'HNSW_INDEX';

-- Restore the HNSW_BUILD_MEM_SIZE parameter value as needed after index creation is complete
ALTER SYSTEM SET HNSW_BUILD_MEM_SIZE = 64M;
```

## Common Vector Queries

### Approximate Nearest Neighbor Query

Use vector distance functions to query the most similar records to a given vector.

```sql
-- Query approximate nearest neighbors using L2_DISTANCE (Euclidean distance)
SELECT doc_id, content,
       L2_DISTANCE(embedding, TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32)) AS distance
FROM vector_table
ORDER BY distance
FETCH APPROX FIRST 3 ROWS ONLY;

-- Query approximate nearest neighbors using COSINE_DISTANCE (cosine distance)
SELECT doc_id, content,
       COSINE_DISTANCE(embedding, TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32)) AS distance
FROM vector_table
ORDER BY distance
FETCH APPROX FIRST 3 ROWS ONLY;

-- Query approximate nearest neighbors using INNER_PRODUCT (dot product distance)
SELECT doc_id, content,
       INNER_PRODUCT(embedding, TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32)) AS distance
FROM vector_table
ORDER BY distance DESC
FETCH APPROX FIRST 3 ROWS ONLY;

-- Query approximate nearest neighbors using VECTOR_DISTANCE function
SELECT doc_id, content,
       VECTOR_DISTANCE(embedding, TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32), COSINE) AS distance
FROM vector_table
ORDER BY distance
FETCH APPROX FIRST 3 ROWS ONLY;

-- Use EXPLAIN to confirm whether the query is accelerated via an index. The HNSW INDEX SCAN operator indicates that index scanning is being used
explain SELECT doc_id, content,
       COSINE_DISTANCE(embedding, TO_VECTOR('[0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]', 10, FLOAT32)) AS distance
FROM vector_table
ORDER BY distance
FETCH APPROX FIRST 3 ROWS ONLY;  

PLAN_DESCRIPTION
----------------------------------------------------------------
SQL hash value: 3040957342
Optimizer: ADOPT_C

+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
| Id | Operation type                 | Name                 | Owner      | Rows     | Cost(%CPU)  | Partition info                 |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+
|  0 | SELECT STATEMENT               |                      |            |          |             |                                |
|  1 |  WINDOW                        |                      |            |         3|        1( 0)|                                |
|  2 |   TABLE ACCESS BY INDEX ROWID  | VECTOR_TABLE         | VECTOR_USER|         3|        1( 0)|                                |
|* 3 |    HNSW INDEX SCAN             | HNSW_INDEX           | VECTOR_USER|          |             |                                |
+----+--------------------------------+----------------------+------------+----------+-------------+--------------------------------+

Operation Information (identified by operation id):
---------------------------------------------------

   1 - Limit Expression: (LIMIT: 3)
   3 - Predicate : access(VECTOR_DISTANCE("VECTOR_TABLE"."EMBEDDING", VECTOR, COSINE))

17 rows fetched.
```

### Vector Norm Calculation

Use the VECTOR_NORM function to calculate the norm of a vector.

```sql
-- Calculate L2 norm of vectors
SELECT doc_id, VECTOR_NORM(embedding) AS l2_norm
FROM vector_table;
```

### Get Vector Dimension

Use the VECTOR_DIMENSION_COUNT function to get the dimension of vectors.

```sql
-- Get vector dimension
SELECT VECTOR_DIMENSION_COUNT(embedding) AS dimension
FROM vector_table
FETCH APPROX FIRST 1 ROWS ONLY;
```

### Vector Format Conversion

Use the FROM_VECTOR function to convert vectors to string format.

```sql
-- Convert vectors to strings
SELECT doc_id, content, FROM_VECTOR(embedding RETURNING VARCHAR(4000)) AS embedding_str
FROM vector_table;
```

## Index Management

```sql
-- View vector indexes for current user
SELECT index_name, index_type, table_name, status
FROM user_indexes
WHERE index_type = 'VECTOR';

-- Drop vector index
DROP INDEX hnsw_index;

-- Modify vector index visibility
ALTER INDEX hnsw_index VISIBLE;

-- Modify vector index to unusable
ALTER INDEX hnsw_index UNUSABLE;

-- Rename vector index
ALTER INDEX hnsw_index RENAME TO new_hnsw_index;
```
