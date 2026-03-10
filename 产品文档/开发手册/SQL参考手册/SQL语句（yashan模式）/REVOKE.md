通用描述
----

REVOKE用于对某一用户或角色收回授予其的权限，可收回的内容包括[系统特权SYSTEM PRIVILEGE](../../../产品安全/数据访问控制/特权与角色管理（yashan模式）/系统特权)、[对象特权OBJECT PRIVILEGE](../../../产品安全/数据访问控制/特权与角色管理（yashan模式）/对象特权)和[角色ROLE](../../../产品安全/数据访问控制/特权与角色管理（yashan模式）/角色)。

收回授予给用户上的权限立即生效。

收回授予给角色上的权限立即生效，如该角色已被授权给某个用户，则该用户的权限收回也立即生效。

收回授予给用户或角色的角色在用户下一次连接时生效，即收回用户A的角色B，收回前已登录的用户A依然具备B角色，A用户再次登录时失去B角色。

普通用户只能收回自己授出的权限，即使对于指定了WITH GRANT|ADMIN OPTION语句实现跨用户授权的场景，也不能跨用户收回权限。具备SECURITY_ADMIN安全管理员角色的用户可收回任何权限。

YashanDB支持在单机和共享集群部署中收回授予给角色的角色，在分布式部署中执行此操作则会返回错误。

YashanDB的权限体系管理具体描述请查阅[特权与角色管理](../../../产品安全/数据访问控制/特权与角色管理（yashan模式）/00特权与角色管理（yashan模式）)。

语句定义
----

**revoke::=**

```ebnf+diagram
syntax::= revoke_system_privilege|revoke_object_privilege|revoke_role
```

**[revoke\_system\_privilege](#revoke_system_privilege)::=**

```ebnf+diagram
syntax::= REVOKE (system_privilege|ALL PRIVILEGES) FROM (user|role)
```

**[revoke\_object\_privilege](#revoke_object_privilege)::=**

```ebnf+diagram
syntax::= REVOKE (object_privilege|ALL PRIVILEGES) ON [schema "."] object_name  FROM (user|role)
```

**revoke\_role::=**

```ebnf+diagram
syntax::= REVOKE role FROM (user|role)
```

<span id="revoke_system_privilege" name="revoke_system_privilege" class="yaslink"></span>
### 1. revoke\_system\_privilege

该语句用于将指定的系统特权从用户/角色收回。

执行该语句的用户（权限收回操作执行者）的要求如下：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 具备GRANT ANY PRIVILEGE权限的普通用户。

- 若某个普通用户通过WITH ADMIN OPTION语句获得了权限转授权，那么该用户可以收回自己转授出的权限。

#### 1.1. system\_privilege

将收回的系统特权的名称，单条语句最多可以收回300个系统特权，多个名称间用逗号分隔。

若指定ALL PRIVILEGES，表示收回所有系统权限。

查询DBA_SYS_PRIVS视图，可了解系统特权的授权记录，并确认需要收回的权限信息。

#### 1.2. user\_name|role

被收回权限的对象，需指定为已创建的用户/角色名称。

示例

```sql
--在系统用户下将授予sales1用户的ALTER SYSTEM权限收回
REVOKE ALTER SYSTEM FROM sales1;
```

<span id="revoke_object_privilege" name="revoke_object_privilege" class="yaslink"></span>
### 2. revoke\_object\_privilege

该语句用于将指定的对象特权从用户/角色收回。

执行该语句的用户（权限收回操作执行者）的要求如下：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 具备GRANT ANY OBJECT PRIVILEGE权限的普通用户。

- 若某个普通用户通过WITH GRANT OPTION语句获得了权限转授权，那么该用户可以收回自己转授出的权限。

#### 2.1. object\_privilege

将收回的对象特权的名称，单条语句最多可以收回300个对象权限，多个名称间用逗号分隔。

若指定ALL PRIVILEGES，表示收回所有对象特权。

#### 2.2. object\_name

对象的名称，需指定为已创建的表或[自定义类型](../../PL参考手册/PL对象/自定义类型)。

#### 2.3. user\_name|role

被收回权限的对象，需指定为已创建的用户/角色名称。

示例

```sql
-- 在用户sales下将表area的SELECT权限授予sales1用户
GRANT SELECT ON area TO sales1 WITH GRANT OPTION;

-- 此时sales1将具备表area的SELECT权限，并可以再将此权限授予其他用户
conn sales1/1%2
GRANT SELECT ON sales.area TO sales2;

-- 在用户sales下收回其授予给sales1的SELECT ON area权限
conn sales/sales
REVOKE SELECT ON area FROM sales1;

-- sales2的SELECT ON sales.area权限由sales1授权，因此只能由sales1或系统用户收回
conn sales1/1%2
REVOKE SELECT ON sales.area FROM sales2;
```

示例（单机、共享集群部署）

```sql
-- 在用户sales下收回授予给sales1的EXECUTE ON udt_object权限
REVOKE EXECUTE ON udt_object FROM sales1;
```

<span id="revoke_role" name="revoke_role" class="yaslink"></span>
### 3. revoke\_role

该语句用于将指定的角色从用户/角色收回。

执行该语句的用户（权限收回操作执行者）的要求如下：

- 若打开三权分立功能，具备SECURITY_ADMIN安全管理员角色的用户。

- 若关闭三权分立功能，具备SECURITY_ADMIN安全管理员角色或DBA角色的用户。

- 具备GRANT ANY ROLE权限的普通用户。

- 若某个普通用户通过WITH ADMIN OPTION语句获得了权限转授权，那么该用户可以收回自己转授出的权限。

#### 3.1. role

将收回的角色，需指定为已创建的角色名称。

#### 3.2. user\_name|role

被收回角色的对象，需指定为已创建的用户/角色名称。

示例

```sql
--在用户sales下收回其授予给sales1的ya_rol1角色权限
REVOKE ya_rol1 FROM sales1;
```
