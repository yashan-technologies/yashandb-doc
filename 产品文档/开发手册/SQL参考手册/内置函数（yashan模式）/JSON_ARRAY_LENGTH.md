```ebnf+diagram
json_array_length::= JSON_ARRAY_LENGTH "(" json_value ")"
```

JSON_ARRAY_LENGTH函数返回一个JSON数组数据的长度。

**json_value**

json_value为一个二进制[json](../通用SQL语法/json)数据，可通过[JSON](./JSON JSON_PARSE)函数获取。

当json_value为JSON Array类型时，函数返回数组的长度；当json_value为JSON Object/String/Number/Boolean/Null/扩展格式类型时，函数返回NULL；当json_value为NULL时，函数返回NULL。


示例

```sql
SELECT JSON_ARRAY_LENGTH(JSON('[123, "ABC", false]')) res FROM DUAL;
                  RES
---------------------
                    3

SELECT JSON_ARRAY_LENGTH(JSON('{"ID": 1}')) res FROM DUAL;
                  RES
---------------------


```
