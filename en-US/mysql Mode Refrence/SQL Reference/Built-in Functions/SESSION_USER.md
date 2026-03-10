```ebnf+diagram
session_user::= SESSION_USER "("")"
```

The SESSION_USER function returns a string combination of the username and hostname used by the client to connect to the database. This function is a synonym for the [USER](USER) function.

This function has no parameters and returns a value of type VARCHAR.

***Example*** for  Heap tables

```sql
SELECT SESSION_USER() res FROM DUAL;
res               
----------------- 
SYS@localhost
```
