通用描述
----

LOCK TABLE用于对表、视图或物化视图以指定模式加表锁，执行该操作的用户需对操作对象具备相应权限：

- 操作对象属于当前用户。
- 操作对象不属于当前用户：

    - 操作对象属于sys用户，当前用户需可访问视图，对表具备INSERT、DELETE或UPDATE对象权限中的任意一种。

    - 操作对象属于非sys用户，当前用户需可访问视图，对表具备除READ外的任何对象权限。

LOCK TABLE不适用于存算一体分布式集群部署。

语句定义
----

**lock table::=**

```ebnf
= LOCK TABLE ([ schema'.' ](table | view)){','([ schema'.' ](table | view))} IN lockmode MODE[NOWAIT|(WAIT integer)]';'.
```

### lockmode

该字段用于指定表锁类型。

**SHARE**

共享表锁允许其他事务对该表加共享锁，但不允许其他事务对该表进行DDL操作。

**EXCLUSIVE**

排他表锁仅允许其他事务对表进行查询，禁止其他事务对该表进行DML操作或对表加任何锁。

### waitmode

该字段用于指定加锁操作是否进行等待以及等待超时的判断条件，等待期间不再具备当前会话的控制权（即无法输入新语句）。

若不指定WAIT或NOWAIT，数据库会无限期地等待直至可对表进行加锁，然后还原会话控制权。

**NOWAIT**

指定NOWAIT模式，数据库会在无法加锁时立即返回错误信息。

**WAIT**

指定WAIT模式，数据库会以integer值作为可等待的超时上限，若等待达到时间上限后仍无法加锁，返回错误信息。

integer需指定为整数值，取值范围[0,2147483647]，单位为秒。

示例（单机/共享集群/分布式集群部署）

```sql
-- 对表进行加锁
LOCK TABLE area,department IN SHARE MODE NOWAIT;

-- 对视图进行加锁
LOCK TABLE v_area IN EXCLUSIVE MODE WAIT 100;
```
