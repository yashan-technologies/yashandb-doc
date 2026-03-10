## 通用描述

ALTER DATABASE用于修改数据库的相关属性。

语句定义
----

**alter database::=**

```ebnf+diagram
syntax::= ALTER DATABASE database_name [DEFAULT] [CHARACTER SET charset_name] [DEFAULT] [COLLATE collation_name]
```

示例（单机HEAP表）

```sql
ALTER DATABASE sales DEFAULT CHARACTER SET 'utf8mb4';

```