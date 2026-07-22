## General Description

ALTER INDEX is used to modify the relevant information of an index.


## Statement Definition

**alter index::=**

```ebnf
= ALTER INDEX [schema"."] index_name (INITRANS integer|(VISIBLE|INVISIBLE)|UNUSABLE|(NOPARALLEL|PARALLEL [integer])|RENAME TO new_name | rebuild_clause ) 
{" " (INITRANS integer|(VISIBLE|INVISIBLE)|UNUSABLE|(NOPARALLEL|PARALLEL [integer])|RENAME TO new_name | rebuild_clause )}.
```

**rebuild_clause::=**

```ebnf
= REBUILD (TABLESPACE (tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|(NOPARALLEL|PARALLEL [integer]) ) 
{" " (TABLESPACE (tablespace_name|DEFAULT)|INITRANS integer|PCTFREE integer|(NOPARALLEL|PARALLEL [integer]) )}.
```

Currently, it is allowed to modify the INITRANS, VISIBLE, and PARALLEL parameters of an index, rename the index, invalidate the index, or rebuild the index. For detailed parameter descriptions, please refer to [CREATE VECTOR INDEX](./CREATE VECTOR INDEX).
