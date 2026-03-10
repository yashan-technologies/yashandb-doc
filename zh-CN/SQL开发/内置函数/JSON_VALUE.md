```ebnf+diagram
json_value::= JSON_VALUE "(" expr [FORMAT JSON] ","  json_path ")"
```
JSON_VALUE函数将基于json_path所描述的路径对json_value进行检索，并返回对应的标量值。

本函数遵循如下规则：

- 当expr为NULL或转换JSON类型失败时，函数返回NULL。
- 函数根据当前路径表达式查询得出的结果包含多个值时，函数返回NULL。
- 路径表达式末尾指定了json_function_step并且返回多个值时，函数返回NULL。
- 返回的value为object或array时，函数返回NULL。

**expr**

expr支持字符串类型，LOB，BFILE，RAW，JSON类型；在运算之前会先转成JSON类型，转换失败后函数返回NULL。

**FORMAT JSON**

用于语法兼容，无实际含义，可省略。

**json_path**

路径表达式，为一个常量字符串，其格式定义请参考[json](../../全部手册/开发手册/SQL参考手册/通用SQL语法/json)文档中描述。

示例（HEAP表）
```sql
SELECT JSON_VALUE('{"key4":-0.123,"key5":"test"}','$.key4') res FROM DUAL;
RES
----------------------------------------------------------------
-0.123     

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$."a".a.a') res FROM DUAL;
RES
----------------------------------------------------------------
1 

SELECT JSON_VALUE('{"a":{"a":{"a":1}}}', '$.."a".COUNT()') res FROM DUAL;
RES
----------------------------------------------------------------
3    

--创建用于存储json数据的表table_json
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
