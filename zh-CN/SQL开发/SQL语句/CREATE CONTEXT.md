通用描述
----

CREATE CONTEXT用于新建上下文对象。具有create any context权限的所有用户均可执行本语句创建上下文对象，但上下文对象始终为sys用户所有。

语句定义
----

**create context::=**

```ebnf+diagram
syntax::= CREATE [OR REPLACE] CONTEXT namespace USING [ schema. ] package
```

### 1. namespace

该语句用于指定创建的上下文的名称，不可省略，且需符合YashanDB的[对象命名规范](../../全部手册/开发手册/SQL参考手册/基本SQL元素/标识符)。

### 2. schema

该语句用于指定创建上下文关联包用户名称。

### 3. package

该语句用于指定创建上下文关联包名称。

该语句需满足如下规则：

- 创建上下文不校验包用户和包是否有效。
- 创建上下文不支持关联内置高级包DBMS_SESSION。

示例 （单机/共享集群/分布式集群部署）

```sql
CREATE CONTEXT ctx1 USING REGRESS.CTX_PKG1;
```
