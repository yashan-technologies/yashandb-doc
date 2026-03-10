```ebnf+diagram
check_user_privilege::= CHECK_USER_PRIVILEGE "(" user_id "," privilege_id ")"
```

CHECK_USER_PRIVILEGE函数检测某用户是否具有指定的权限，返回值为BOOLEAN类型。

**user_id**

用户ID，必须是整数类型的数据，使用[USERENV](../../../开发手册/SQL参考手册/内置函数/USERENV)函数或查询[DBA_USERS](../../../参考手册/系统视图/DBA视图/DBA_USERS)视图获取。

**privilege_id**

权限ID，必须是整数类型的数据，查询[DBA_ROLES](../../../参考手册/系统视图/DBA视图/DBA_ROLES)视图获取。

示例（HEAP表）

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT CHECK_USER_PRIVILEGE(0,19) pri FROM DUAL;
pri
--------------------
true
```
