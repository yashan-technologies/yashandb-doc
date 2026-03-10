```ebnf+diagram
json_query::= JSON_QUERY "(" json_value [FORMAT JSON] "," "'" json_path "'" format_clause ")"

format_clause::= [returning_clause] [PRETTY] [wrapper_clause]

returning_clause::= RETURNING "VARCHAR(size)"

wrapper_clause::= (WITH|WITHOUT) [ARRAY] WRAPPER
```
The JSON_QUERY function retrieves data from json_value based on the path described by json_path and outputs the retrieved results according to the display options defined in format_clause.

**json_value**

json_value is a binary [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) data, which can be obtained using the [JSON](./JSON JSON_PARSE) function. When json_value is NULL, the function returns NULL.

**FORMAT JSON**

Used for syntax compatibility, has no actual meaning, can be omitted.

**json_path**

A path expression, which is a constant string; its format is defined in the [json](../../All Manuals/Development Guide/SQL Reference Manual/General SQL Syntax/json) documentation.

**returning_clause**

Refer to the description of returning_clause in [json_serialize](./JSON_SERIALIZE JSON_FORMAT).

**PRETTY**

Refer to the description of PRETTY in [json_serialize](./JSON_SERIALIZE JSON_FORMAT).

**wrapper_clause**

Specifies whether to wrap the retrieved results as a JSON Array type, where WITH [ARRAY] WRAPPER indicates wrapping and WITHOUT [ARRAY] WRAPPER indicates no wrapping.

When this clause is omitted, the default is WITHOUT ARRAY WRAPPER, indicating no wrapping.

In the following two cases, WITH [ARRAY] WRAPPER must be specified for wrapping; otherwise, the function will return an error.

- The result obtained from the current path expression contains multiple values.
- The path expression ends with json_function_step.

***Example***
```sql
SELECT JSON_QUERY(JSON('{"key4":-0.123,"key5":"test"}'),'$') res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
{"key4":-0.123,"key5":"test"}      

SELECT JSON_QUERY(JSON('{"a":{"a":{"a":1}}}'), '$.."a"' WITH ARRAY WRAPPER) res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
[{"a":{"a":1}},{"a":1},1]     

SELECT JSON_QUERY(JSON('{"1":1,"2":2,"3":3}'), '$."1"') res FROM DUAL;
RES                                             
---------------------------------------------------------------- 
1      

-- Create a table to store json data named table_json
CREATE TABLE IF NOT EXISTS table_json (id INT, c1 VARCHAR(300));
INSERT INTO table_json VALUES(0, '{"key1": 123, "key2": true, "key3": null, "key4": [456, false, null, {"key1": true, "key2": 789, "key3": {"key6": 123}}, [10, false, null]], "key5": {"key1": true, "key2": 789, "key3": null}}');

SELECT JSON_QUERY(JSON(c1), '$.key4[last]') res FROM table_json ORDER BY id;
RES                                            
---------------------------------------------------------------- 
[10,false,null] 

SELECT JSON_QUERY(JSON(c1), '$.key4[0]' WITH WRAPPER) res FROM table_json ORDER BY id;
RES                                             
---------------------------------------------------------------- 
[456]  

SELECT JSON_QUERY(JSON(c1), '$[0].key4[0][0][0]' WITH WRAPPER) res FROM table_json ORDER BY id;
RES                                             
---------------------------------------------------------------- 
[456]  
```
