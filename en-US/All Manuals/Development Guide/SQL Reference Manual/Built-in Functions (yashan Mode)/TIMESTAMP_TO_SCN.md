```ebnf+diagram
timestamp_to_scn::= TIMESTAMP_TO_SCN "(" expr ")"
```

The TIMESTAMP_TO_SCN function converts the SCN data represented by [expr](../General SQL Syntax/expr) into an SCN number.
    
The value of expr must be of TIMESTAMP type, timezone type, or a character type that conforms to the TIMESTAMP format; otherwise, it returns type not supported or format conversion error.

expr cannot be NULL.

***Example***

```sql
SHOW PARAMETER DATE_FORMAT

NAME                                  VALUE
------------------------------------- --------------------------------------
DATE_FORMAT                           YYYY-MM-DD HH24:MI:SS

-- The return format of the SYSDATE function depends on the configuration of the DATE_FORMAT parameter

SELECT SYSDATE,TIMESTAMP_TO_SCN(TO_CHAR(SYSDATE)) scn1,SYSTIMESTAMP,TIMESTAMP_TO_SCN(SYSTIMESTAMP) scn2 FROM DUAL;

SYSDATE                                           SCN1 SYSTIMESTAMP                                                 SCN2
-------------------------------- --------------------- -------------------------------------------- --------------------
2025-07-02 11:11:54                 711021010944000000 2025-07-02 11:11:54.405516 +08:00              711021012604993536
```
