```ebnf
json_query = JSON_QUERY "(" json_value [FORMAT JSON] "," "'" json_path "'" format_clause ")".

format_clause = [returning_clause] [PRETTY] [wrapper_clause].

returning_clause = RETURNING "VARCHAR(size)".

wrapper_clause = (WITH|WITHOUT) [ARRAY] WRAPPER.
```
JSON_QUERY函数将基于json_path所描述的路径对json_value进行检索，并将检索到的结果按照format_clause定义的显示选项进行封装并打印。

**json_value**

json_value为一个二进制[json](../通用SQL语法/json)数据，可通过[JSON](./JSON JSON_PARSE)函数获取。当json_value为NULL时，函数返回NULL。

**FORMAT JSON**

用于语法兼容，无实际含义，可省略。

**json_path**

路径表达式，为一个常量字符串，其格式定义请参考[json](../通用SQL语法/json)文档中描述。

**returning_clause**

参见[json_serialize](./JSON_SERIALIZE JSON_FORMAT.md)中returning_clause描述。

**PRETTY**

参见[json_serialize](./JSON_SERIALIZE JSON_FORMAT.md)中PRETTY描述。

**wrapper_clause**

指定是否将检索结果封装为一个 JSON Array类型数据，其中WITH [ARRAY] WRAPPER表示封装，WITHOUT [ARRAY] WRAPPER表示不封装。

本语句省略时，默认为WITHOUT ARRAY WRAPPER不封装。

在如下两种情况中，必须指定WITH [ARRAY] WRAPPER进行封装，否则函数将返回错误。

- 函数根据当前路径表达式查询得出的结果包含多个值。
- 路径表达式末尾指定了json_function_step。

示例
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

--创建用于存储json数据的表table_json
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
