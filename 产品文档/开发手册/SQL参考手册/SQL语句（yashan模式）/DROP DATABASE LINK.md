通用描述
----

DROP DATABASE LINK语句用于删除一个数据库链接对象。

根据数据库系统的结构，支持两种数据库链接：

- 同构数据库链接：YashanDB与YashanDB的数据库链接
- 异构数据库链接：YashanDB与Oracle的数据库链接，需要进行[异构数据库链接配置](../../../数据库管理/基本数据库管理/异构数据库链接配置)

语句定义
----

**[drop database link](#dropdatabaselink)::=**

```ebnf+diagram
syntax::= DROP [PUBLIC] DATABASE LINK dblink_name
```

<span id="dropdatabaselink" name="dropdatabaselink" class="yaslink"></span>

### 1. PUBLIC

该语句删除一个公有数据库链接，对所有数据库用户可见。

删除公有数据库链接的用户须拥有DROP PUBLIC DATABASE LINK系统权限，删除非公有数据库链接用户须拥有DROP DATABASE LINK系统权限，否则返回错误。

示例

```sql
DROP PUBLIC DATABASE LINK dblink_yashan;
```

### 2. dblink\_name

该语句用于指定创建的数据库链接的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

示例

```sql
-- YashanDB与YashanDB的数据库链接
DROP DATABASE LINK dblink_yashan;

-- YashanDB与Oracle的数据库链接
DROP DATABASE LINK dblink_oracle;
```