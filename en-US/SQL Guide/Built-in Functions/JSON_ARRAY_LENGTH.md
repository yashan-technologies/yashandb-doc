```ebnf+diagram
json_array_length::= JSON_ARRAY_LENGTH "(" json_value ")"
```

The JSON_ARRAY_LENGTH function returns the length of a JSON array data.

**json_value**

json_value is a binary [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) data that can be obtained through the [JSON](./JSON JSON_PARSE) function.

When json_value is of JSON Array type, the function returns the length of the array; when json_value is of JSON Object/String/Number/Boolean/Null/extended format type, the function returns NULL; when json_value is NULL, the function returns NULL.

***Example***

```sql
SELECT JSON_ARRAY_LENGTH(JSON('[123, "ABC", false]')) res FROM DUAL;
                  RES
---------------------
                    3

SELECT JSON_ARRAY_LENGTH(JSON('{"ID": 1}')) res FROM DUAL;
                  RES
---------------------
```
