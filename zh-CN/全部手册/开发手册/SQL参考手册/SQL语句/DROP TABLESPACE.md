通用描述
----

DROP TABLESPACE用于删除一个表空间。

该语句有如下约束限制：

- 不能删除一个已被某个用户指定的DEFAULT TABLESPACE，必须先通过[ALTER USER](./ALTER USER)语句将其变更。
- 不能删除一个已被某个实例指定的DEFAULT SWAP TABLESPACE，必须先通过ALTER SYSTEM SET DEFAULT_SWAP_TABLESPACE语句将其变更。
- 不能删除一个已创建了任何数据库对象的表空间，除非指定了INCLUDING CONTENTS语句。
- 如果表空间中的分区表或分区索引存在至少一个分区在存储参数里指定了其他的表空间，那么即使指定了INCLUDING CONTENTS语句，该表空间也不能被删除。
- 如果表空间中存在被其他表空间对象定义的外键约束，那么即使指定了INCLUDING CONTENTS语句，该表空间也不能被删除，除非同时指定了CASCADE CONSTRAINT。
- 不能删除SYSTEM/SYSAUX/UNDO/SWAP/TEMPORARY等内置表空间。

存算一体分布式集群部署中删除表空间出现节点故障时，恢复措施见[用户表空间管理](../../../数据库管理/存储管理/逻辑空间管理/表空间管理/用户表空间管理)章节描述。

在存算一体分布式集群部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

集群部署中删除表空间出现实例故障时，可能会产生没有数据文件的残留表空间，残留表空间状态是OFFLINE，无法正常使用，只能执行删除语句将该表空间删除。

语句定义
----

**drop tablespace::=**

```ebnf+diagram
syntax::= DROP TABLESPACE [IF EXISTS] tablespace_name [INCLUDING CONTENTS [(AND|KEEP) DATAFILES] [CASCADE CONSTRAINTS]]
```

### 1. IF EXISTS

该语句用于指定在DROP表空间之前，先判断该表空间是否存在。省略则不会判断，此时如果要删除的表空间不存在，系统将提示错误。

### 2. tablespace\_name

该语句用于指定要删除的表空间名称。

### 3. INCLUDING CONTENTS

该语句用于指定将表空间及其包含的所有对象都删除。

#### 3.1. AND|KEEP DATAFILES

指定在删除表空间时，其对应的数据文件是被删除，还是被保留，缺省为保留。

#### 3.2. CASCADE CONSTRAINTS

当存在其他表空间里的对象定义了到本表空间里对象的外键约束时，先将这些外键约束项删除，再删除本表空间。

示例

```sql
DROP TABLESPACE yashan1;

DROP TABLESPACE IF EXISTS yashan2;

DROP TABLESPACE yashan3
INCLUDING CONTENTS
KEEP DATAFILES
CASCADE CONSTRAINTS;
```
