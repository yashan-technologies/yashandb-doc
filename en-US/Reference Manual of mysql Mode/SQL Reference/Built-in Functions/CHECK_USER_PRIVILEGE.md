```ebnf
check_user_privilege = CHECK_USER_PRIVILEGE "(" user_id "," privilege_id ")".
```

The CHECK\_USER\_PRIVILEGE function checks if a user has the specified privilege, returning a BOOLEAN type.

**user\_id**

The user ID must be an integer type of data, obtained using the [USERENV]) function or by querying the [DBA_USERS](../../../Reference Manual/System Views/DBA Views/DBA_USERS) view.

**privilege\_id**

The privilege ID must be an integer type of data, obtained by querying the [DBA_ROLES](../../../Reference Manual/System Views/DBA Views/DBA_ROLES) view.

***Example*** for  Heap tables

```sql
alter session set COMPAT_VECTOR = mysql;
SELECT CHECK_USER_PRIVILEGE(0,19) pri FROM DUAL;
pri
--------------------
true
```
