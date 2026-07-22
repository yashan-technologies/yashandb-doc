通用描述
----

DROP SYNONYM用于删除一个同义词。

存算一体分布式集群部署中用户无法执行本语句。

语句定义
----

**drop synonym::=**

```ebnf
= DROP [PUBLIC] SYNONYM synonym_name.
```

### PUBLIC

删除一个公共同义词时，此语句不可省略，必须指定。

### synonym\_name

该语句用于指定要删除的同义词的名称。

示例（单机/共享集群/分布式集群部署）

```sql
--删除公共同义词
DROP PUBLIC SYNONYM sy_area1;
 
--删除本用户下的私有同义词
DROP SYNONYM sy_area2;
```
