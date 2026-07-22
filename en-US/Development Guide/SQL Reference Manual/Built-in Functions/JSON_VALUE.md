```ebnf
json_value = JSON_VALUE "(" expr [FORMAT JSON] ","  json_path ")".
```
The JSON_VALUE function retrieves the scalar value from json_value based on the path described by json_path.

This function follows these rules:

- When expr is NULL or conversion to JSON type fails, the function returns NULL.
- When the results obtained from the current path expression contain multiple values, the function returns NULL.
- If the path expression ends with json_function_step and returns multiple values, the function returns NULL.
- When the returned value is an object or array, the function returns NULL.

**expr**

expr supports data types such as STRING, LOB, BFILE, RAW, and JSON; it will be converted to JSON type before operations, failing which the function returns NULL.

**FORMAT JSON**

This clause is for syntax compatibility only and has no actual meaning; it can be omitted.

**json_path**

The path expression is a constant string, and its format definition can be referred to in the [json](../General SQL Syntax/json) documentation.

***Example*** for Heap tables
```sql
SELECT JSON_VALUE('{"key4":-0.123,"key5":"test"}','$.key4') res FROM DUAL;
RES
----------------------------------------------------------------
-0.123     

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$."a".a.a') res FROM DUAL;
RES
----------------------------------------------------------------
1 

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$.."a".count()') res FROM DUAL;
RES
----------------------------------------------------------------
3    

-- Create a table to store json data table_json
CREATE TABLE IF NOT EXISTS table_json (id INT, c1 VARCHAR(300));
INSERT INTO table_json VALUES(0, '{"key1": 123, "key2": true, "key3": null, "key4": [456, false, null, {"key1": true, "key2": 789, "key3": {"key6": 123}}, [10, false, null]], "key5": {"key1": true, "key2": 789, "key3": null}}');

SELECT JSON_VALUE(c1, '$.key4.key1') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
true

SELECT JSON_VALUE(c1, '$.key4[last]') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
  

SELECT JSON_VALUE(JSON(c1), '$[0].key4[0][0][0]') res FROM table_json ORDER BY id;
RES
----------------------------------------------------------------
456
```
