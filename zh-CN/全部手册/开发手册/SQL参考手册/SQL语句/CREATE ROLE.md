通用描述
----

CREATE ROLE用于创建一个角色。

角色管理属于权限体系的一部分，YashanDB的角色管理体系请参考[角色管理](../../../产品安全/数据访问控制/特权与角色管理/角色)。YashanDB包含如下两种类型的角色：

*   系统内置角色：如DBA角色和PUBLIC角色。
*   普通角色：通过本语句创建的普通角色。


在容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE）中，若连接根容器使用本语句，表示创建全局角色。


语句定义
----

**create role::=**

```ebnf+diagram
syntax::= CREATE ROLE role [SLOT slot_id] [container "=" (CURRENT|ALL)]
```

### 1. role

该语句用于指定要创建的角色的名称，不可省略，且需符合YashanDB的[对象命名规范](../基本SQL元素/标识符)。


在容器数据库中，全局角色的名称必须以前缀开头，默认前缀为C##，前缀值可在创建全局角色、全局用户以及全局profile前通过COMMON_USER_PREFIX参数配置。本地角色的名称始终不能以C##以及自定义指定的前缀开头。  


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

### 3. container



该语句仅适用于容器数据库（配置参数ENABLE_PLUGGABLE_DATABASE=TRUE），用于指定当前操作的容器作用域，可指定全局生效或当前容器本地生效，可省略。

CONTAINER属性可选值如下：

- CURRENT：表示仅在当前所连接的容器本地生效。直连PDB时只能指定为该值或直接省略，效果相同。  

- ALL：表示全局生效。仅连接根容器时可用且省略时默认为该值。



示例（单机/共享集群/分布式集群部署）

```sql
CREATE ROLE c##manager container = ALL;
```
