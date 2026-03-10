通用描述
----

DROP TABLESPACE SET语句用于删除一个表空间集。

该语句有如下约束限制：

- 该语句仅适用于分布式部署。
- 不能删除已被某个用户指定为默认表空间集的表空间集，如仍需删除必须先通过 [ALTER USER](./ALTER USER) 语句变更该用户的 DEFAULT TABLESPACE SET 。
- 不能删除内置表空间集USERS/USERS_AIM。

在分布式部署中，执行本语句时系统会检查是否存在异常未恢复DDL语句，若存在将报错并需等待对应DDL恢复成功后才能执行本语句。

语句定义
----

**drop tablespace set::=**

```ebnf+diagram
syntax::= DROP TABLESPACE SET tablespace_set_name INCLUDING CONTENTS
```

### 1. tablespace\_set\_name

该语句用于指定要删除的表空间集的名称。

### 2. INCLUDING CONTENTS

该语句用于指定将表空间集及其包含的所有对象都删除。

表空间集挂载的bucket中数据文件尚未完成归档清理时，该bucket无法删除，此时删除该表空间集会返回错误。

示例（分布式部署）

```sql
DROP TABLESPACE SET tbs_tb INCLUDING CONTENTS;
DROP TABLESPACE SET mm_tss INCLUDING CONTENTS;
DROP TABLESPACE SET tss1 INCLUDING CONTENTS;
```

