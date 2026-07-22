通用描述
----

GRANT用于对某一用户或角色授予权限，可授予的内容包括[系统特权SYSTEM PRIVILEGE](../../../产品安全/数据访问控制/特权与角色管理/系统特权)、[对象特权OBJECT PRIVILEGE](../../../产品安全/数据访问控制/特权与角色管理/对象特权)和[角色ROLE](../../../产品安全/数据访问控制/特权与角色管理/角色)。

在同一条GRANT赋权语句中，支持向一个用户同时进行系统级权限和角色的赋权，不允许对象级权限与系统级权限或角色的混合式赋权。

授予给用户的权限立即生效。

授予给角色的权限立即生效，若该角色已被授权给某个用户，则该用户的权限也立即生效。

授予给用户或角色的角色在用户下一次登录时生效，即给用户A授予角色B，授权前已登录的用户A不具备该角色，A用户再次登录时角色B生效。

YashanDB的权限体系管理具体描述请查阅[特权与角色管理](../../../产品安全/数据访问控制/特权与角色管理/00特权与角色管理.md)。

语句定义
----

**grant::=**

```ebnf
= grant_system_privilege|grant_object_privilege|grant_role.
```

**[grant\_system\_privilege](#grant_system_privilege)::=**

```ebnf
= GRANT ((system_privilege {"," system_privilege}) | (ALL PRIVILEGES)) TO ((user_name [WITH ADMIN OPTION])|role).
```

**[grant\_object\_privilege](#grant_object_privilege)::=**

```ebnf
= GRANT (((object_privilege) {"," (object_privilege)}) | (ALL PRIVILEGES)) ON [schema "."] object_name TO ((user_name [WITH GRANT OPTION])|role).
```

**[grant_role](#grant_role)::=**

```ebnf
= GRANT (role {"," role}) TO ((user_name [WITH ADMIN OPTION])|role).
```

<span id="grant_system_privilege" name="grant_system_privilege"></span>

### grant\_system\_privilege

该语句用于对用户或角色授予系统特权。

执行该语句的用户（授权操作执行者）的要求如下：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 被授予相应系统特权时，同时指定了WITH ADMIN OPTION语句的普通用户。

- 具备GRANT ANY PRIVILEGE系统特权的普通用户。

#### system_privilege

将授予的系统特权的名称，单条语句最多可以授予300个系统特权和角色，多个名称间用逗号分隔，系统特权清单请查阅[系统特权](../../../产品安全/数据访问控制/特权与角色管理/系统特权)。

若指定ALL PRIVILEGES，表示授予所有系统特权。

#### user\_name|role

被授权者，即用户或角色名称。

#### WITH ADMIN OPTION

将系统特权授予某一个用户时，指定本语句表示该用户具备了对此权限的管理权限，即可以将该系统特权再转授给其他用户或角色。

示例

```sql
-- 在用户sales下将下列权限同时授予sales1用户，并指定WITH ADMIN OPTION
GRANT ALTER SYSTEM, CREATE USER, ALTER USER, DROP USER TO sales1 WITH ADMIN OPTION;
 
-- 此时sales1可以将上述权限授予其他用户
conn sales1/1%2
GRANT ALTER SYSTEM TO sales2;
```

<span id="grant_object_privilege" name="grant_object_privilege"></span>

### grant\_object\_privilege

该语句用于对用户或角色授予对象特权。

某个对象的对象特权建议由其所属用户进行管理，此外，还有下列用户可以执行该语句管理对象权限：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 被授权相应权限时，同时指定了WITH GRANT OPTION语句的普通用户。

- 具备GRANT ANY OBJECT PRIVILEGE系统特权的普通用户。

#### object_privilege

将授予的对象特权的名称，单条语句最多可以授予300个权限，多个名称间用逗号分隔，对象特权清单请查阅[OBJECT PRIVILEGE](../../../产品安全/数据访问控制/特权与角色管理/对象特权)。

若指定ALL PRIVILEGES，表示授予所有对象特权。

#### object\_name

对象的名称，需指定为已创建的表、存储过程、高级包、自定义函数或自定义类型，不允许使用别名。

#### user\_name|role

被授权者，即用户或角色名称。

#### WITH GRANT OPTION

将对象特权授予某一个用户时，指定本语句表示该用户可以将该对象特权再转授给其他的用户或角色。

示例

```sql
-- 在用户sales下将表area的SELECT、UPDATE权限授予sales1用户并指定WITH GRANT OPTION
GRANT SELECT,UPDATE ON area TO sales1 WITH GRANT OPTION;

-- 此时sales1将具备表area的SELECT和UPDATE权限，并可以再将权限授予其他用户
conn sales1/1%2
GRANT SELECT ON sales.area TO sales2;
```

示例（单机/共享集群/分布式集群部署）

```sql
-- 在用户sales下将自定义类型udt_object的EXECUTE权限授予sales1用户
GRANT EXECUTE ON udt_object TO sales1;
```

<span id="grant_role" name="grant_role"></span>

### grant\_role

该语句用于对用户或角色授予角色。

在存算一体分布式集群部署中，无法对角色授予角色。

执行该语句的用户（授权操作执行者）的要求如下：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 被授权角色时，同时指定了WITH ADMIN OPTION语句的普通用户。

- 具备GRANT ANY ROLE系统特权的普通用户。

#### role

将授予的角色名称，存算一体分布式集群部署下单条语句最多可以授予8个角色，其他部署形态下单条语句最多可以授予300个系统特权和角色，多个名称间用逗号分隔，所有角色请查询[DBA_ROLES](../../../参考手册/系统视图/DBA视图/DBA_ROLES)视图。

#### user\_name|role

被授权者，即用户或角色名称。

#### WITH ADMIN OPTION

将角色某一个用户时，指定本语句表示该用户可以将该角色再转授给其他的用户或角色。

> **Note**: 
>
> 授权SYSDBA和SYSOPER角色时，指定WITH ADMIN OPTION无效。

示例（单机/共享集群/分布式集群部署）

```sql
-- 在sales用户下创建角色ya_rol1并授予其area表的SELECT权限
CREATE ROLE ya_rol1;
GRANT SELECT ON area TO ya_rol1;
 
-- 将ya_rol1角色权限授予sales1用户，则在sales1重新连接后，即可以具备sales.area的SELECT权限，此操作需要sales用户具备GRANT ANY ROLE权限
-- 由于未指定关键字WITH ADMIN OPTION，sale1不能将角色ya_rol1授予其他用户
GRANT ya_rol1 TO sales1;

-- 在sales用户下创建角色ya_rol2并授予其CREATE TABLE的权限，将ya_rol2授予ya_rol1, 由于ya_rol1已经授予sales1用户，则sales1重新连接后，可具备ya_rol2角色
CREATE ROLE ya_rol2;
GRANT CREATE TABLE TO ya_rol2;
GRANT ya_rol2 to ya_rol1;

-- 重新将ya_rol1角色权限授予sales1用户并指定关键字WITH ADMIN OPTION，此时sales1用户可将ya_rol1角色授予其他用户
REVOKE ya_rol1 FROM sales1;
GRANT ya_rol1 TO sales1 WITH ADMIN OPTION;
```
