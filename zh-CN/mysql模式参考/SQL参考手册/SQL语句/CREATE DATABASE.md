## 通用描述

CREATE DATABASE语句用于创建一个新的数据库，该语句只能在数据库启动至open阶段且切换至mysql模式后执行。

## 语句定义

**create database::=**

```ebnf+diagram
syntax::= CREATE DATABASE [IF NOT EXISTS] database_name [CHARACTER SET charset_name] [COLLATE collation_name] 
```

示例（HEAP表）

```sql
CREATE DATABASE IF NOT EXISTS sales DEFAULT CHARACTER SET `utf8` DEFAULT collate `utf8mb4_general_ci`;
```
