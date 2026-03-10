```ebnf+diagram
connection_id::= CONNECTION_ID "("")"
```

The CONNECTION_ID function returns the session id, and the return type is BIGINT.

The CONNECTION_ID value is same as the ID column in the `INFORMATION_SCHEMA.PROCESSLIST` or the ID value after excuting `SHOW PROCESSLIST`.

***Example*** for Heap tables

```sql
 SELECT CONNECTION_ID() AS id3;

                  id3
---------------------
                   34
```
