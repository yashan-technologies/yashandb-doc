## 通用描述

REVOKE用于对某一用户收回授予其的权限，可收回的内容包括[系统特权SYSTEM PRIVILEGE](../../产品安全/特权管理/系统特权)、[模式特权SCHEMA PRIVILEGE](../../产品安全/特权管理/模式特权.md)和[对象特权OBJECT PRIVILEGE](../../产品安全/特权管理/对象特权)。

收回授予给用户上的权限立即生效。

普通用户只能收回自己授出的权限，即使对于指定了WITH GRANT OPTION语句实现跨用户授权的场景，也不能跨用户收回权限。

## 语句定义

**revoke::=**

```ebnf
= revoke_system_privilege|revoke_schema_privilege|revoke_object_privilege.
```

**[revoke\_system\_privilege](#revoke_system_privilege)::=**

```ebnf
= REVOKE (system_privilege) {"," system_privilege} ON "*.*" FROM user_name.
```

**[revoke\_schema\_privilege](#revoke_schema_privilege)::=**

```ebnf
= REVOKE (schema_privilege) {"," schema_privilege} ON [schema "."]"*"  FROM user.
```

**[revoke\_object\_privilege](#revoke_object_privilege)::=**

```ebnf
= REVOKE (object_privilege) {"," object_privilege} ON [TABLE] [schema "."] table_name FROM user_name.
```

<span id="revoke_system_privilege" name="revoke_system_privilege"></span>

### revoke\_system\_privilege

该语句用于将指定的系统特权从用户收回。

#### system_privilege

将收回的系统特权的名称，单条语句最多可以收回300个系统特权和角色，多个名称间用逗号分隔。

查询MYSQL.USER、MYSQL.DB或INFORMATION_SCHEMA.USER_PRIVILEGES视图，可了解系统特权的授权记录，并确认需要收回的权限信息。

#### user\_name

用户名称。

<span id="revoke_schema_privilege" name="revoke_schema_privilege"></span>

### revoke\_schema\_privilege

该语句用于将指定的模式特权从用户收回。

#### schema_privilege

将收回的系统特权的名称，多个名称间用逗号分隔。

schema级别权限对于sys schema不生效。

查询INFORMATION_SCHEMA.SCHEMA_PRIVILEGES视图，可了解模式特权的授权记录，并确认需要收回的权限信息。

#### [schema.]*

模式名称，需指定为已创建的模式。

#### user\_name

用户名称。

<span id="revoke_object_privilege" name="revoke_object_privilege"></span>

### revoke\_object\_privilege

该语句用于将指定的对象特权从用户收回。

#### object_privilege

将收回的对象特权的名称，多个名称间用逗号分隔。

查询MYSQL.TABLES_PRIV或INFORMATION_SCHEMA.TABLE_PRIVILEGES视图，可了解对象特权的授权记录，并确认需要收回的权限信息。

#### [schema.]table_name

表的名称，需指定为已创建的表。

#### user\_name

用户名称。
