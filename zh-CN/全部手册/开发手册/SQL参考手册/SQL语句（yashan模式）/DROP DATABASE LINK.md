通用描述
----

DROP DATABASE LINK语句用于删除一个数据库链接对象。

删除公有数据库链接的用户须拥有DROP PUBLIC DATABASE LINK系统权限，删除非公有数据库链接用户须拥有DROP DATABASE LINK系统权限。

语句定义
----

**[drop database link](#dropdatabaselink)::=**

```ebnf+diagram
syntax::= DROP [PUBLIC] DATABASE LINK dblink_name
```

<span id="dropdatabaselink" name="dropdatabaselink" class="yaslink"></span>

### 1. PUBLIC

仅当删除公有数据库链接时，需指定该关键字。

示例

```sql
DROP PUBLIC DATABASE LINK dblink_yashan;
```

### 2. dblink\_name

已存在的数据库链接名称。

示例

```sql
-- 删除普通数据库链接
DROP DATABASE LINK dblink_yashan;

-- 删除公有数据库链接
DROP PUBLIC DATABASE LINK dblink_oracle;
```
