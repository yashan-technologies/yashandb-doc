通用描述
----

REVOKE用于对某一用户收回授予其的权限，可收回的内容包括[系统特权SYSTEM PRIVILEGE](../../../产品安全/数据访问控制/特权管理（mysql模式）/系统特权)、[模式特权SCHEMA PRIVILEGE](../../../产品安全/数据访问控制/特权管理（mysql模式）/模式特权)和[对象特权OBJECT PRIVILEGE](../../../产品安全/数据访问控制/特权管理（mysql模式）/对象特权)。

收回授予给用户上的权限立即生效。

普通用户只能收回自己授出的权限，即使对于指定了WITH GRANT OPTION语句实现跨用户授权的场景，也不能跨用户收回权限。

语句定义
----

**revoke::=**

```ebnf+diagram
syntax::= revoke_system_privilege|revoke_schema_privilege|revoke_object_privilege
```

**[revoke\_system\_privilege](#revoke_system_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((system_privilege)) {"," (system_privilege)} ON "*.*" FROM user_name
```

**[revoke\_schema\_privilege](#revoke_schema_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((schema_privilege)) {"," (schema_privilege)} ON [schema "."]"*"  FROM user
```

**[revoke\_object\_privilege](#revoke_object_privilege)::=**

```ebnf+diagram
syntax::= REVOKE ((object_privilege)) {"," (object_privilege)} ON [TABLE] [schema "."] table_name FROM user_name
```

<span id="revoke_system_privilege" name="revoke_system_privilege" class="yaslink"></span>

### 1. revoke\_system\_privilege

该语句用于将指定的系统特权从用户收回。

#### 1.1. system\_privilege

将收回的系统特权的名称，单条语句最多可以收回300个系统特权和角色，多个名称间用逗号分隔。

查询MYSQL.USER、MYSQL.DB或INFORMATION_SCHEMA.USER_PRIVILEGES视图，可了解系统特权的授权记录，并确认需要收回的权限信息。

#### 1.2. user\_name

用户名称。

<span id="revoke_schema_privilege" name="revoke_schema_privilege" class="yaslink"></span>

### 2. revoke\_schema\_privilege

该语句用于将指定的模式特权从用户收回。

#### 2.1. schema\_privilege

将收回的系统特权的名称，多个名称间用逗号分隔。

schema级别权限对于sys schema不生效。

查询INFORMATION_SCHEMA.SCHEMA_PRIVILEGES视图，可了解模式特权的授权记录，并确认需要收回的权限信息。

#### 2.2. [schema.]*

模式名称，需指定为已创建的模式。

#### 2.3. user\_name

用户名称。

<span id="revoke_object_privilege" name="revoke_object_privilege" class="yaslink"></span>

### 3. revoke\_object\_privilege

该语句用于将指定的对象特权从用户收回。

#### 3.1. object\_privilege

将收回的对象特权的名称，多个名称间用逗号分隔。

查询MYSQL.TABLES_PRIV或INFORMATION_SCHEMA.TABLE_PRIVILEGES视图，可了解对象特权的授权记录，并确认需要收回的权限信息。

#### 3.2. [schema.]table\_name

表的名称，需指定为已创建的表。

#### 3.3. user\_name

用户名称。
