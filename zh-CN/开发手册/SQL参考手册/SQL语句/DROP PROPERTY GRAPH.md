通用描述
----

DROP PROPERTY GRAPH用于删除数据库中已存在的属性图对象。

存算一体分布式集群部署中用户无法执行该语句。

语句定义
----

**drop property graph::=**

```ebnf
= DROP PROPERTY GRAPH [schema "."] graph_name [IF EXISTS].
```

### IF EXISTS

指定IF EXISTS表示如果属性图不存在，不报错，静默跳过删除操作。

### graph_name

指定要删除的属性图名称。

示例（单机/共享集群/分布式集群部署）

```sql
-- 示例1：删除属性图
DROP PROPERTY GRAPH social_network;

-- 示例2：属性图不存在时不报错（使用IF EXISTS）
DROP PROPERTY GRAPH IF EXISTS non_existent_graph;

-- 示例3：删除带schema的属性图
DROP PROPERTY GRAPH myschema.my_graph;
```