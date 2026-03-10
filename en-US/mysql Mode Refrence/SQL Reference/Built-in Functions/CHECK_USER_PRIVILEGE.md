```ebnf+diagram
check_user_privilege::= CHECK_USER_PRIVILEGE "(" user_id "," privilege_id ")"
```

The CHECK_USER_PRIVILEGE function checks if a user has the specified privilege, returning a BOOLEAN type.

**user_id**

The user ID must be an integer type of data, obtained using the [USERENV]) function or by querying the [DBA_USERS](../../../All Manuals/Reference Manual/System Views/DBA Views/DBA_USERS) view.

**privilege_id**

The privilege ID must be an integer type of data, obtained by querying the [DBA_ROLES](../../../All Manuals/Reference Manual/System Views/DBA Views/DBA_ROLES) view.

***Example*** for  Heap tables

```sql
ALTER SESSION SET COMPAT_VECTOR = mysql;
SELECT CHECK_USER_PRIVILEGE(0,19) pri FROM DUAL;
pri
--------------------
true
```
