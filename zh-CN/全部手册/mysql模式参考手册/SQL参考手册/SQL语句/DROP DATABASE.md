## 通用描述

DROP DATABASE用于删除当前数据库，删除数据库所包含的持久化数据文件。

## 语句定义

**drop database::=**

```ebnf+diagram
syntax::= DROP DATABASE [if exists] database_name
```

示例（单机HEAP表）

```sql
DROP DATABASE [IF EXISTS] sales;

```
