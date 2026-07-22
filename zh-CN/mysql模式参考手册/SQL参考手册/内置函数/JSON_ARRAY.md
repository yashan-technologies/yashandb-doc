```ebnf
json_array = JSON_ARRAY "(" [val ["," val] ...] ")".
```

JSON_ARRAY函数用于将一系列值组合成一个JSON格式的数组。

**val**

val为要放入JSON数组中的值，可以是任意类型的值。当val为NULL时，JSON数组中对应位置的值将为null。

最大支持65535个入参。

示例（单机HEAP表）

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