```ebnf
systimestamp = SYSTIMESTAMP ["(" [integer] ")"].
```
The SYSTIMESTAMP function is used to retrieve the current SCN set by the operating system of the database server.

In YashanDB's different deployment forms, this function behaves differently:

- In Standalone and YAC/Distributed Cluster Deployment, the return value includes timezone information (the timezone set by the operating system), and the return type is TIMESTAMP WITH TIME ZONE, consistent with the format specified by the TIMESTAMP_TZ_FORMAT parameter.

    >**Caution**:
    >
    > TIMESTAMP WITH TIME ZONE is a new data type added in YashanDB 23.4. To successfully execute this function, the client (including *yasql*, driver interfaces, etc.) must be version 23.4 or above, along with a compatible version of 23.4.

- In ISC Distributed Cluster Deployment, the return type is TIMESTAMP, consistent with the format specified by the TIMESTAMP_FORMAT parameter.

If a SQL statement contains multiple SYSTIMESTAMP functions, this function will only be called once during the execution of the statement, returning the same SCN value.

**integer**

It must be a literal integer between 0 and 9, indicating the number of decimal places to retain, rounding the discarded digits according to the usual rounding rules.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT SYSTIMESTAMP res1,SYSTIMESTAMP() res2,SYSTIMESTAMP(9) res3 FROM DUAL;
res1                                                             res2                                                             res3
---------------------------------------------------------------- ---------------------------------------------------------------- ----------------------------------------------------------------
2025-02-21 11:41:32.018118 +08:00                                2025-02-21 11:41:32.018118 +08:00                                2025-02-21 11:41:32.018118 +08:00
```

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT SYSTIMESTAMP res1,SYSTIMESTAMP() res2,SYSTIMESTAMP(9) res3 FROM DUAL;
RES1                          RES2                          RES3               
----------------------------- ----------------------------- ----------------------------- 
2022-11-02 03:11:43.579228    2022-11-02 03:11:43.579228    2022-11-02 03:11:43.579228
```
