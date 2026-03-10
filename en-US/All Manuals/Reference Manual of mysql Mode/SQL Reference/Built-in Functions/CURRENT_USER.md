```ebnf+diagram
current_user::= CURRENT_USER "("")"
```

The CURRENT_USER function returns a string that combines the username and hostname used by the server to authenticate the current client.

This function has no input parameters, and its return type is VARCHAR.

***Example*** for  Heap tables

```sql
SELECT CURRENT_USER() res FROM DUAL;
res       
--------- 
SYS@% 
```
