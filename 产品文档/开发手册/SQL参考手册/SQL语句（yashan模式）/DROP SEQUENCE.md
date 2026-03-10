通用描述
----

DROP SEQUENCE用于删除一个已存在的序列号生成器。

当序列号生成器已被其他数据库对象引用时，删除这个序列号生成器可能会引起一些错误，或者让对象失效。例如：

*   如在某张表的列字段上引用序列号生成器定义了DEFAULT值，则在该序列号生成器被删除后，对该表使用DEFAULT生成列字段的值时，提示YAS-02012错误。
*   如在某张视图的定义中指定了序列号生成器作为一个列字段，则在该序列号生成器被删除后，该视图失效。

分布式部署中用户无法执行本语句。

语句定义
----

**drop sequence::=**

```ebnf+diagram
syntax::= DROP SEQUENCE [schema "."] sequence_name
```

示例（单机、共享集群部署）

```sql
DROP SEQUENCE seq_yashan1;
```
