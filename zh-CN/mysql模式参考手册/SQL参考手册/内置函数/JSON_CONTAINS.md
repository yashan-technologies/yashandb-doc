```ebnf
json_contains = JSON_CONTAINS "(" target "," candidate ["," path] ")".
```

JSON_CONTAINS函数用于判断给定的JSON文档`condidate`是否包含在目标JSON文档`target`中；如果函数中输入了`path`参数，将以目标JSON文档`target`的指定路径判断是否存在`candidate`JSON文档。函数返回[0|1|null]，类型为BIGINT类型，返回1表示匹配，返回0表示不匹配，任意入参为null时函数返回null。

当满足以下规则时，判断`condidate`包含在目标JSON文档`target`中：

- 当且仅当两个标量值可比较且相等时，`candidate`标量才包含在`target`标量中。两个标量值可比较的前提是它们具有相同的JSON类型，但是INTEGER和DECIMAL类型的值也可以相互比较。 
- 当且仅当`candidate`数组中的每个元素都包含在`target`数组的某个元素中时，才会判断为包含。
- 当且仅当`candidate`包含在`target`数组的某个元素中时，非数组类型的`candidate`才包含在`target`数组中。
- 当且仅当`candidate`对象中的每个键在`target`对象中都存在同名的键，且`candidate`键所关联的值包含在`target`键所关联的值中时，`candidate`对象才包含在`target`对象中。

**target**

`target`为二进制[json](../通用SQL语法/json)数据，表示目标JSON文档。

入参的数据类型可以为JSON、CHAR、VARCHAR、TEXT、BLOB或RAW类型。

当`target`为NULL时，函数返回NULL。

**candidate**

`candidate`为二进制[json](../通用SQL语法/json)数据，表示要查找的JSON文档。

入参的数据类型可以为JSON、CHAR、VARCHAR、TEXT、BLOB或RAW类型。

当`candidate`为NULL时，函数返回NULL。

**path**

`path`为可选参数，指定在`target`中查找`candidate`的路径。

入参的数据类型可以为CHAR、VARCHAR或TEXT类型。

当`path`为NULL时，函数返回NULL。

当`target`不存在path路径时，函数返回NULL。

示例（单机HEAP表）

```sql
SELECT JSON_CONTAINS('{"a": 1, "b": 2}', '2', '$.*') AS result;

               result
---------------------
                    1

CREATE TABLE t1(id int, js1 varchar(255), js2 varchar(255));

INSERT INTO t1 VALUES(1, '{"a": 1, "b": 2, "c": {"d": 4}}', '1');

SELECT JSON_CONTAINS(js1, js2, '$.a') res FROM t1 where id = 1;

                  res
---------------------
                    1

SELECT JSON_CONTAINS(js1, js2, '$.b') res FROM t1 where id = 1;

                  res
---------------------
                    0

INSERT INTO t1 VALUES(2, '{"d": 4}', '1');

SELECT JSON_CONTAINS(js1, js2, '$.a') res FROM t1 where id = 2;

                  res
---------------------


SELECT JSON_CONTAINS(js1, js2, '$.c') res FROM t1 where id = 2;

                  res
---------------------


```