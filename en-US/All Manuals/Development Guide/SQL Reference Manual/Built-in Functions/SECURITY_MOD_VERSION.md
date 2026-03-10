```ebnf+diagram
SECURITY_MOD_VERSION::= SECURITY_MOD_VERSION"()" 
```

SECURITY_MOD_VERSION is used to obtain and display the password module version information.

***Example***

```sql
SELECT SECURITY_MOD_VERSION() FROM dual;

SECURITY_MOD_VERSION                          
--------------------------------------------- 
Yashan Database Management System Password Module V23              

```
