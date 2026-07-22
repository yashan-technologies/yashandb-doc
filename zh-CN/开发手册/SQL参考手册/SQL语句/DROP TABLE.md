通用描述
----

DROP TABLE语句用于删除表对象（包括建立在该表上的索引、约束、触发器等对象），同时该表的数据被删除，该表及索引所占数据空间被全部释放。

在[回收站开启](../../../数据库管理/故障处理/闪回/回收站对象闪回/00回收站对象闪回.md)（配置参数RECYCLEBIN_ENABLED为ON）的场景下执行DROP语句删除HEAP表且未指定PURGE关键字，该表及其索引、约束、触发器、LOB列等数据都将进入回收站（表名称被修改为BIN开头的系统生成字符串）；删除TAC表、LSC表以及外部表则始终表现为彻底删除。

DROP操作删除了表对象的所有相关定义，如需再次使用该表，只能按创建新表的方式进行重建。

DROP操作不能回滚（Rollback），也不能通过[flashback\_query\_clause](./SELECT)获得操作之前的数据。如果指定了PURGE语句，也无法从回收站恢复。

当需要DROP被子表定义了外键约束的父表时，需要指定CASCADE CONSTRAINTS。

当需要DROP已建立了AC的源表时，需先DROP该表上全部的AC后，才可以DROP该表。

语句定义
----

**drop table::=**

```ebnf
= DROP TABLE [IF EXISTS] [schema "."] table_name [CASCADE CONSTRAINTS] [PURGE].
```

### IF EXISTS

该语句用于指定在DROP表时忽略表不存在的错误。

示例

```sql
--DROP不存在的表area_drop
DROP TABLE IF EXISTS area_drop;
```

### CASCADE CONSTRAINTS

该语句只作用于HEAP表，用于指定当表被子表定义了外键约束时，先删除子表上的外键约束项，再DROP该表。

示例（HEAP表）

```sql
--area的子表branches表上定义的外键约束被删除
DROP TABLE area CASCADE CONSTRAINTS;
```

### PURGE

该语句用于指定彻底删除表，而不是进入回收站（ReclycleBin）。

存算一体分布式集群部署中无法使用本语句。

如果显式指定了PURGE，则DROP的表对象及数据不会进入回收站，而是被彻底删除，无法恢复。

示例（HEAP表）

```sql
DROP TABLE finance_info PURGE;
```
