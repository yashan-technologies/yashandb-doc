```ebnf
check_sys_privilege = CHECK_SYS_PRIVILEGE "(" user_id "," object_type_id ")".
```

The CHECK_SYS_PRIVILEGE function checks whether the logged-in user has access class system-level privileges on a certain type of object belonging to a specific user, returning a BOOLEAN type value.

This function does not support vectorization calculation.

When checking privileges on table-type objects, if the user has any of the privileges SELECT ANY TABLE, INSERT ANY TABLE, UPDATE ANY TABLE, DELETE ANY TABLE, the function returns TRUE; otherwise, it returns FALSE.

**user\_id**

The OWNER user ID of the object, which must be an integer type. It can be obtained using the [USERENV](USERENV) function or by querying the [DBA_USERS](../../../Reference Manual/System Views/DBA Views/DBA_USERS) view.

**object\_type\_id**

The type ID of the object, which must be an integer type. You can refer to the OBJECT_TYPE field description in the [DBA_OBJECTS](../../../Reference Manual/System Views/DBA Views/DBA_OBJECTS) view; for example, the type ID for tables is 1.

***Example***

```sql
SELECT CHECK_SYS_PRIVILEGE(0,1) pri FROM DUAL;
PRI
--------------------
true
```
