```ebnf
tz_offset = TZ_OFFSET "(" expr ")".
```

The TZ\_OFFSET function returns the timezone offset of the input parameter relative to UTC based on the date when the statement is executed.

**expr**

Supports two types of input:

- A timezone offset relative to UTC. This input will return itself. It can be of any type, but if it cannot be converted to string format and does not conform to the timezone format, an error will be raised.

- The keywords SESSIONTIMEZONE or DBTIMEZONE.

***Example***

```sql
SELECT TZ_OFFSET('+08:00') res FROM DUAL;
RES
--------------------
+08:00
```
