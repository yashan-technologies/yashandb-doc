```ebnf
current_timestamp = CURRENT_TIMESTAMP  ["(" [integer] ")"] .
```
The CURRENT_TIMESTAMP function is used to obtain the current time, specifically the current session SCN.

In different deployment forms of YashanDB, this function performs differently:

- In Standalone and YAC/Distributed Cluster Deployment, the return value includes timezone information (session timezone), and the return type is TIMESTAMP WITH TIME ZONE.

    >**Caution**:
    >
    > The TIMESTAMP WITH TIME ZONE is a new data type added in YashanDB 23.4. Successful execution of this function requires the client (including *yasql*, driver interfaces, etc.) to be version 23.4 or higher and compatible with 23.4.

- In ISC Distributed Cluster Deployment, the return type is TIMESTAMP.

**integer**

Must be an integer literal between 0 and 9, representing the number of microseconds to retain, with discarded digits rounded to the nearest value.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT current_timestamp() res FROM DUAL;
RES
----------------------------------------------------------------
2022-04-12 18:37:26.008000
```

***Example*** for ISC Distributed Cluster Deployment
```sql
SELECT current_timestamp() res FROM dual;
RES
----------------------------------------------------------------
2022-04-12 18:37:26.008000
```
