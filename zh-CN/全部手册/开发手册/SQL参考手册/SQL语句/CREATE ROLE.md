通用描述
----

CREATE ROLE用于创建一个角色。角色管理属于权限体系的一部分，YashanDB的角色管理体系请参考产品安全手册[角色管理](../../../产品安全/数据访问控制/特权与角色管理/角色)。

YashanDB包含如下两种类型的角色：

*   系统内置角色：如DBA角色和PUBLIC角色。
*   普通角色：通过本语句创建的普通角色。

语句定义
----

**create role::=**

```ebnf+diagram
syntax::= CREATE ROLE role [SLOT slot_id]
```

### 1. role

该语句用于指定要创建的角色的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。

示例

```sql
CREATE ROLE rolename;
```

### 2. SLOT

若YashanDB某个新版本新增了对象特权或系统特权，数据库升级到该版本后需要手动创建对应权限并指定该语句绑定权限名称与对应ID，**需在原厂工程师指导下使用**。

示例

```sql
CREATE ROLE privilegename SLOT 10;
```
