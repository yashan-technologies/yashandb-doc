```ebnf+diagram
json_exists::= JSON_EXISTS "(" json_value "," "'"json_path"'" ")"
```

The JSON_EXISTS function searches for json_value based on the path described by json_path. It returns TRUE if the corresponding query result is not empty; otherwise, it returns FALSE.

**json_value**

json_value is a binary [json](../General SQL Syntax/json) data that can be obtained using the [JSON](./JSON JSON_PARSE) function. When json_value is NULL, the function returns NULL.

**json_path**

The path expression is a constant string, and its format definition can be found in the [json](../General SQL Syntax/json) documentation.

***Example***
```sql
CREATE TABLE IF NOT EXISTS table_json (id INT, c1 VARCHAR(300));
INSERT INTO table_json VALUES(0, '{"key1": 123, "key2": true, "key3": null, "key4": [456, false, null, {"key1": true, "key2": 789, "key3": {"key6": 123}}, [10, false, null]], "key5": {"key1": true, "key2": 789, "key3": null}}');

SELECT JSON_EXISTS(JSON(c1), '$') res FROM table_json ORDER BY id;
RES 
-------------------- 
true   

SELECT JSON_EXISTS(JSON(c1), '$.key1') res FROM table_json ORDER BY id;
RES 
-------------------- 
true    

SELECT JSON_EXISTS(JSON(c1), '$.key4[10]') res FROM table_json ORDER BY id;
RES 
-------------------- 
false 
```
