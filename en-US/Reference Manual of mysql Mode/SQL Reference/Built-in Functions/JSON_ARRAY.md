```ebnf
json_array = JSON_ARRAY "(" [val ["," val] ...] ")".
```

The JSON_ARRAY function is used to combine a series of values into a JSON-formatted array.

**val**

val is the value to be placed in the JSON array, which can be of any type. When val is NULL, the corresponding position in the JSON array will be null.

Maximum support for 65535 input parameters.

***Example*** for Standalone Deployment Heap tables

```sql
SELECT JSON_ARRAY(1, 'abc', NULL, TRUE, CURTIME()) res FROM DUAL;

res
----------------------------------------------------------------
[1,"abc",null,true,"14:38:40"]

SELECT JSON_ARRAY(NOW(3)) FROM DUAL;

JSON_ARRAY(NOW(3))
--------------------------------------
["2026-03-30 10:31:16.185"]
```