```ebnf+diagram
json_exists::= JSON_EXISTS "(" json_value "," "'"json_path"'" ")"
```

JSON_EXISTS函数基于json_path所描述的路径对json_value进行查找，若对应查询结果不为空则返回TRUE，否则返回FALSE。

**json_value**

json_value为一个二进制[json](../../全部手册/开发手册/SQL参考手册/通用SQL语法/json)数据，可通过[JSON](./JSON JSON_PARSE)函数获取。当json_value为NULL时，函数返回NULL。

**json_path**

路径表达式，为一个常量字符串，其格式定义请参考[json](../../全部手册/开发手册/SQL参考手册/通用SQL语法/json)文档中描述。

示例
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
