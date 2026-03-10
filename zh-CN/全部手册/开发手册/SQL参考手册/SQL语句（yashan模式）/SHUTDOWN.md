## 通用描述

SHUTDOWN用于关闭当前正在运行的数据库。

只有拥有SYSDBA或SYSOPER角色的用户才可执行SHUTDOWN操作。

语句定义
----

**shutdown::=**

```ebnf+diagram
syntax::= SHUTDOWN [NORMAL | IMMEDIATE | ABORT] [WAIT STANDBY]
```

### 1. NORMAL

默认的关闭选项，必须等待现有的会话结束后才执行关闭，在数据库重启之后不会进行实例恢复。

### 2. IMMEDIATE

尽可能快速的关闭数据库，不会等待现有的会话结束，但是会回滚未提交的事务。

此种关闭模式下所有的客户端会话将被断开，且数据库重启之后不会进行实例恢复。

### 3. ABORT

尽可能快速的关闭数据库，不会等待现有的会话结束，也不回滚未提交的事务。

此种关闭模式下所有的客户端会话将被断开，在数据库重启之后会进行实例恢复。

### 4. WAIT STANDBY

该选项表示关闭数据库前，日志要同步到所有备库，并且等待被备库回放。

示例

```sql
SHUTDOWN;

SHUTDOWN IMMEDIATE;

SHUTDOWN ABORT;

SHUTDOWN WAIT STANDBY;
```
