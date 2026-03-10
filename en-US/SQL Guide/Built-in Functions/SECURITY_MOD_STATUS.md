```ebnf+diagram
SECURITY_MOD_STATUS::= SECURITY_MOD_STATUS"()" 
```

SECURITY_MOD_STATUS is used to retrieve and display the current security mode.

***Example***

```sql
SELECT SECURITY_MOD_STATUS() FROM dual;

SECURITY_MOD_STATUS(  
--------------------- 
Approved Mode Status         

```
