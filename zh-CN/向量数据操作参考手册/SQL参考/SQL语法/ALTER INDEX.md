## 通用描述

ALTER INDEX用于修改索引的相关信息。


## 语句定义

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

当前允许修改索引的INITRANS、VISIBLE、PARALLEL参数，对索引重新命名，失效索引或重建索引。详细的参数说明请参考[CREATE VECTOR INDEX](CREATE VECTOR INDEX.md)。
