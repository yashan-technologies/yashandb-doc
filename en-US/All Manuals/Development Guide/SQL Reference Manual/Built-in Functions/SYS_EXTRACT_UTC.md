```ebnf+diagram
sys_extract_utc::= SYS_EXTRACT_UTC "(" datetime ")"
```

The SYS_EXTRACT_UTC function is mainly used to convert the input datetime to the corresponding UTC (Coordinated Universal Time) and returns a Timestamp type data.

This function follows the rules below:

- datetime must be of Timestamp or time zone type.

***Example***

```sql
SELECT SYS_EXTRACT_UTC(TIMESTAMP'2000-02-01 4:00:00') res FROM dual;

RES
----------------------------------------------------------------
2000-01-31 20:00:00.000000

```
