## 通用描述

GRANT用于对某一用户授予权限，可授予的内容包括[系统特权SYSTEM PRIVILEGE](../../产品安全/特权管理/系统特权)、[模式特权SCHEMA PRIVILEGE](../../产品安全/特权管理/模式特权)和[对象特权OBJECT PRIVILEGE](../../产品安全/特权管理/对象特权)。

授予给用户的权限立即生效。

## 语句定义

**grant::=**

```ebnf
= grant_system_privilege|grant_schema_privilege|grant_object_privilege.
```

**[grant\_system\_privilege](#grant_system_privilege)::=**

```ebnf
= GRANT (system_privilege) {"," system_privilege} ON "*.*" TO user_name [WITH GRANT OPTION].
```

**[grant\_schema\_privileg](#grant_schema_privilege)e::=**

```ebnf
= GRANT (schema_privilege) {"," schema_privilege} ON [schema "."]"*"  TO user_name [WITH GRANT OPTION].
```

**[grant\_object\_privilege](#grant_object_privilege)::=**

```ebnf
= GRANT (object_privilege) {"," object_privilege} ON [TABLE] [schema "."] table_name TO user_name [WITH GRANT OPTION].
```

<span id="grant_system_privilege" name="grant_system_privilege"></span>

### grant\_system\_privilege

该语句用于对用户授予系统特权。

#### system_privilege

将授予的系统特权的名称，多个名称间用逗号分隔，系统特权清单请查阅[系统特权](../../产品安全/特权管理/系统特权)。

#### user\_name

被授权者用户名称。

#### WITH GRANT OPTION

将系统特权授予某一个用户时，指定本语句表示该用户具备了对此权限的管理权限，即可以将该系统特权再转授给其他用户。

示例（HEAP表）

```sql
-- 在用户SALES下将CREATE USER权限授予SALES1用户，并指定WITH GRANT OPTION
GRANT CREATE USER ON *.* TO SALES1 WITH GRANT OPTION;
 
-- 此时SALES1可以将上述权限授予其他用户
conn SALES1/********
GRANT CREATE USER ON *.* TO SALES2;
```

<span id="grant_schema_privilege" name="grant_schema_privilege"></span>

### grant\_schema\_privilege

该语句用于对用户授予模式特权。

模式特权对于sys schema不生效。

#### schema_privilege

将授予的系统特权的名称，多个名称间用逗号分隔，系统特权清单请查阅[模式特权](../../产品安全/特权管理/模式特权.md)。

#### [schema.]*

模式的名称，需指定为已创建的模式。

#### user\_name

被授权者用户名称。

#### WITH GRANT OPTION

将系统特权授予某一个用户时，指定本语句表示该用户具备了对此权限的管理权限，即可以将该系统特权再转授给其他用户。

示例（HEAP表）

```sql
-- 在用户SALES下将指定模式下的所有权限授予SALES1用户
conn SALES/********
GRANT ALL ON sales.* TO SALES1 WITH GRANT OPTION;
```

<span id="grant_object_privilege" name="grant_object_privilege"></span>

### grant\_object\_privilege

该语句用于对用户授予对象特权。

#### object_privilege

将授予的对象特权的名称，多个名称间用逗号分隔，对象特权清单请查阅[OBJECT PRIVILEGE](../../产品安全/特权管理/对象特权)。

#### [schema.]table_name

表的名称，需指定为已创建的表。

#### user\_name

被授权者，即用户名称。

#### WITH GRANT OPTION

将对象特权授予某一个用户时，指定本语句表示该用户可以将该对象特权再转授给其他的用户。

示例（HEAP表）

```sql
-- 在用户SALES1下将指定表的所有权限授予SALES2用户
GRANT ALL ON TABLE sales.sales_info TO SALES2;
```
