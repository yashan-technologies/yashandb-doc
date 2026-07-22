## General Description

CREATE VECTOR INDEX is used to create an index for vector data.

YashanDB supports creating HNSW vector indexes, and only one HNSW vector index is allowed on the same table. Moreover, the vector column for creating an HNSW index must specify the vector dimension number.

HNSW (Hierarchical Navigable Small World) is an efficient algorithm for Approximate Nearest Neighbor (ANN) search in high-dimensional vectors, suitable for large-scale, low-latency retrieval scenarios.

- Vector indexes are not allowed to be created on partitioned table and global temporary table.
- Creating HNSW indexes in ONLINE mode is not currently supported.
- Creating function indexes on vector column is not supported.

After creating a vector index, query performance will only be improved via the index if the SQL query meets the following conditions and using the index results in a lower performance cost.

- The query SQL statement includes both an `order_by_clause` and a `fetch` clause.
- The `order_by_clause` must be based on a vector column for which a vector index has been created, and the distance function of the vector index must match the one used in the query statement.
- The `fetch` clause must specify the APPROXIMATE or APPROX keyword.

## Statement Definition

**create vector index::=**

```ebnf
= CREATE VECTOR INDEX index_name ON table_name "(" vector_column ")" vector_index_attr_clause.
```

**vector_index_attr_clause::=**

```ebnf
= vector_index_organization_clause vector_index_parameters_clause (TABLESPACE ( tablespace_name | DEFAULT ) | INITRANS integer | PCTFREE integer | ( VISIBLE | INVISIBLE ) | ( USABLE | UNUSABLE ) | ( NOPARALLEL | PARALLEL [integer] ) ) *.
```

**vector_index_organization_clause::=**

```ebnf
= [GLOBAL] ORGANIZATION [NEIGHBOR] GRAPH [ WITH ] [ DISTANCE metric_name ].
```

**vector_index_parameters_clause::=**

```ebnf
= [ PARAMETERS "(" vector_index_parameters_hnsw_clause ")" ].
```

**vector_index_parameters_hnsw_clause::=**

```ebnf
=  ( ( ( TYPE HNSW ) | ( ( NEIGHBORS | M ) max_closest_vectors_connected ) | ( EFCONSTRUCTION max_candidates_to_consider ) ) ) ( "," ( ( TYPE HNSW ) | ( ( NEIGHBORS | M ) max_closest_vectors_connected ) | ( EFCONSTRUCTION max_candidates_to_consider ) ) ) *.
```

### index\_name

This statement specifies the name of the index to be created. It cannot be omitted and must comply with YashanDB's [object naming conventions](../../../Development Guide/SQL Reference Manual/Basic SQL Elements/Identifiers).

### table\_name

This statement specifies the table on which to create the index. During the index creation process, an exclusive lock will be applied to that table.

### vector_column

Specifies a named vector column as the index column.

<span id="columnexpression" name="columnexpression"></span>

### vector_index_attr_clause

This statement is used to specify various attributes of the index.

#### vector_index_organization_clause

##### GLOBAL

This statement is used to explicitly specify that the created index is a global index. If the GLOBAL keyword is not specified, YashanDB also defaults to creating a global index. 

The GLOBAL key word is used for compatibility and have no practical meaning.

##### DISTANCE metric_name

This statement is used to specify the vector distance calculation method. If not configured, it defaults to COSINE.

The supported configuration options are as follows:

- EUCLIDEAN: Represents the Euclidean distance between two vectors.
- EUCLIDEAN_SQUARED: Represents the squared Euclidean distance between two vectors (without taking the square root).
- L2_SQUARED: Same as EUCLIDEAN_SQUARED.
- COSINE: Represents the cosine distance between two vectors.
- DOT: Indicates the negative inner product distance between two vectors.

#### vector_index_parameters_clause

This statement is used to specify the calculation parameters for HNSW vector indexes.

##### TYPE HNSW

Configures the vector index algorithm as HNSW. Currently, only HNSW is supported.

##### NEIGHBORS|M

Configures the maximum number of neighbor vectors at any level. M is a synonym for NEIGHBORS. The configuration range is [2, 100], and it defaults to 16 when omitted.

The larger this parameter value, the more vectors are connected at each level, the more memory the index occupies, and the more distance calculations are required during index building and vector search. However, recall and accuracy typically improve.

For high-dimensional or complex datasets, this parameter can be appropriately increased.

##### EFCONSTRUCTION

During the HNSW indexing process, this is the number of candidate vectors filtered by distance similarity before selecting neighbor vectors for different vectors. The range is [4, 1000], and it defaults to 64 when omitted.

When configured, the EFCONSTRUCTION value must be at least twice the NEIGHBORS value.

The larger the EFCONSTRUCTION, the wider the candidate range to be examined before determining M vector neighbors, resulting in a better index graph structure.

Increasing EFCONSTRUCTION will significantly increase the distance calculation and sorting overhead during the construction phase, thereby substantially increasing index build time.

Under normal circumstances, based on the dataset size and accuracy requirements, for example, the EFCONSTRUCTION value can be set to 5 times the expected number of results for the final vector similarity query.

#### TABLESPACE

This statement is used to specify the tablespace of the index. If omitted, the current user's tablespace is used by default.

*   tablespace_name: Specify an existing tablespace.
*   DEFAULT: Specify the default tablespace, which is the tablespace of the current user.

The tablespace for indexes created on temporary tables can only be the system's default temporary tablespace and cannot be specified.

#### INITRANS/PCTFREE

This statement is used to specify the storage properties of the index. If omitted, INITRANS/PCTFREE defaults to 2/8.

#### VISIBLE|INVISIBLE

This statement is used to specify whether the created index can be utilized by the optimizer (Optimizer). If omitted, it defaults to VISIBLE.

#### USABLE|UNUSABLE

This statement is used to specify whether the created index is usable. If omitted, it defaults to USABLE.

#### NOPARALLEL|PARALLEL

This statement is used to set the degree of parallelism for index creation. NOPARALLEL indicates no parallelism. 

If the degree of parallelism is not specified, it defaults to half the number of CPU cores.

**integer**

The degree of parallelism, acceptable values range from [1, server CPU core count * 2]. It can be omitted. If omitted and the current table has a data volume greater than 1G but less than the current DATA_BUFFER_SIZE parameter value, it defaults to half the number of CPU cores.

***Example*** for Heap tables

```sql
CREATE TABLE VECTOR_TABLE(
 DOC_ID BIGINT NOT NULL,
 CHUNK_ID BIGINT NOT NULL,
 EMBEDDING VECTOR(1024, FLOAT32)
) ORGANIZATION HEAP;

CREATE VECTOR INDEX HNSW_INDEX 
  ON VECTOR_TABLE(EMBEDDING) ORGANIZATION NEIGHBOR GRAPH 
  WITH DISTANCE COSINE PARAMETERS(
    TYPE HNSW, 
    M 16, 
    EFCONSTRUCTION 64
  ) PARALLEL 4;
```