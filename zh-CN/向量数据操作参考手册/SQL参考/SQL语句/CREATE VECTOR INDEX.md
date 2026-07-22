## 通用描述

CREATE VECTOR INDEX用于为向量数据创建索引。

YashanDB支持创建HNSW向量索引，同一张表上只允许创建一个HNSW向量索引，且创建HNSW索引的向量列必须指定向量维度数。

HNSW（Hierarchical Navigable Small World，可导航小世界层级图）是一种用于高维向量近似最近邻搜索（ANN）的高效算法，适合大规模、低延迟的检索场景。

- 分区表和全局临时表上不允许创建向量索引。
- 暂不支持ONLINE方式创建HNSW索引。
- 向量列不支持创建函数索引。

创建向量索引后，只有查询SQL语句满足以下条件，且走索引进行查询时性能代价更低才会通过索引提高查询效率。

- 查询SQL语句同时携带`order_by_clause`和`fetch`子句。
- `order_by_clause`必须基于向量列创建了向量索引，且向量索引的距离函数与查询语句相同。
- `fetch`子句中指定APPROXIMATE/APPROX关键字。

## 语句定义

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

该语句用于指定创建的索引的名称，不可省略，且需符合YashanDB的[对象命名规范](../../../开发手册/SQL参考手册/基本SQL元素/标识符)。

### table\_name

该语句用于指定要创建索引的表，创建索引的操作过程中会对该表加排他锁。

### vector_column

将指定名称的向量列字段作为索引列。

<span id="columnexpression" name="columnexpression"></span>

### vector_index_attr_clause

该语句用于配置索引的各项属性。

#### vector_index_organization_clause

##### GLOBAL

该语句用于显式指定创建的索引是全局索引。若不指定该关键字，YashanDB也将默认创建全局索引。

该关键字仅为兼容性适配，无实际含义。

##### DISTANCE metric_name

该语句用于指定向量距离的计算方法，不配置时默认为COSINE。

支持配置项如下：

- EUCLIDEAN：表示计算两个向量的欧式距离。
- EUCLIDEAN_SQUARED：表示计算两个向量的不取平方根的欧式距离。
- L2_SQUARED：同EUCLIDEAN_SQUARED。
- COSINE：表示计算两个向量的余弦距离。
- DOT：表示计算两个向量的负内积距离。

#### vector_index_parameters_clause

该语句用于指定HNSW向量索引的计算参数。

##### TYPE HNSW

配置向量索引算法为HNSW，当前仅支持HNSW。

##### NEIGHBORS|M

配置在任一层上邻居向量的最大个数，M为NEIGHBORS的同义词，配置范围为[2, 100]，省略时默认为16。

该参数配置值越大，每一层上连接的向量越多，索引占用的内存越大，构建索引和向量搜索时的距离计算量也增加，但召回率和精度通常会提升。

对于维度较高或复杂的数据集可适当调高该参数。

##### EFCONSTRUCTION

HNSW索引过程中，为不同向量选择邻居向量前，按照距离相似度筛选的候选向量的数量。范围为[4, 1000]，省略时默认为64。

配置时，要求EFCONSTRUCTION的配置值必须大于等于NEIGHBORS配置值的2倍。

EFCONSTRUCTION越大，在确定M个向量邻居前需要考察的候选范围越广，最终构建出的索引图结构越优。

增大EFCONSTRUCTION会显著增加构建阶段的距离计算和排序开销，从而大幅增加索引构建时间。

通常情况下，需要根据数据集大小和精度要求，例如可以将EFCONSTRUCTION配置值设置为最终向量相似度查询期望返回数量的5倍。

#### TABLESPACE

该语句用于指定索引的表空间，省略则默认为当前用户所在的表空间。

*   tablespace\_name：指定到一个存在的表空间。
*   DEFAULT：指定到缺省表空间，即当前用户所在的表空间。

在临时表上创建的索引，其表空间只能为系统默认的temporary表空间，不可指定。

#### INITRANS/PCTFREE

该语句用于指定索引的存储属性，省略则INITRANS/PCTFREE默认为2/8。

#### VISIBLE|INVISIBLE

该语句用于指定创建的索引是否能被优化器（Optimizer）使用，省略则默认为VISIBLE。

#### USABLE|UNUSABLE

该语句用于指定创建的索引是否可用，省略则默认为USABLE。

#### NOPARALLEL|PARALLEL

该语句用于设置创建索引的并行度，NOPARALLEL表示不并行。

不指定并行度默认按一半CPU核数的并行度并发创建索引。

**integer**

并行度值，取值范围[1,服务器CPU核数\*2]，可省略。省略时，当探测到当前表数据量为大于1G，小于当前DATA_BUFFER_SIZE参数值时，默认按一半CPU核数的并行度并发创建索引。

示例（HEAP表）

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