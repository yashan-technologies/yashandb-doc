```ebnf
SECURITY_MOD_VERSION = SECURITY_MOD_VERSION"()" .
```

SECURITY_MOD_VERSION is used to obtain and display the password module version information.

***Example***

```sql
select SECURITY_MOD_VERSION() from dual;

SECURITY_MOD_VERSION                          
--------------------------------------------- 
Yashan Database Management System Password Module V23              

1 row fetched.
```
