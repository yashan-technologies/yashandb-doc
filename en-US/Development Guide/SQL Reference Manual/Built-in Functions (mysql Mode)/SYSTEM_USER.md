```ebnf+diagram
system_user::= SYSTEM_USER "("")"
```

The SYSTEM_USER function returns a string combination of the username and hostname used by the client to connect to the database. This function is a synonym for the [USER](USER) function.

This function has no parameters and returns a value of type VARCHAR.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT SYSTEM_USER() res FROM DUAL;
res               
----------------- 
SYS@localhost
```
