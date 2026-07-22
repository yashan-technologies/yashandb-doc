```ebnf
json_length = JSON_LENGTH "(" json_doc ["," path] ")".
```

JSON_LENGTH函数返回JSON文档的长度；如果指定了path参数，则返回文档中该路径所标识的值的长度。JSON_LENGTH函数返回值数据类型为BIGINT。

文档长度的计算规则如下： 

- 标量的长度为1。
- 数组的长度为数组元素的数量。
- 对象的长度为对象成员的数量。
- 长度不计算嵌套数组或对象的长度。

**json_doc**

`json_doc`为二进制[json](../通用SQL语法/json)数据，可通过[JSON_ARRAY](JSON_ARRAY)函数生成。

入参的数据类型可以为JSON、CHAR、VARCHAR、TEXT、BLOB或RAW类型。

当`json_doc`为NULL时，函数返回NULL。

**path**

`path`为可选参数，指定`json_doc`文档中的路径。

入参的数据类型可以为CHAR、VARCHAR或TEXT类型。

当`path`为NULL时，函数返回NULL。

当`json_doc`不存在`path`路径时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT JSON_LENGTH('[1, 2, {"a": 3}]') res FROM DUAL;

                  res
---------------------
                    3

SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30, "d": 40}}') res FROM DUAL;

                  res
---------------------
                    2

SELECT JSON_LENGTH('{"a": 1, "b": {"c": 30, "d": 40}}', '$.b') res FROM DUAL;

                  res
---------------------
                    2
```