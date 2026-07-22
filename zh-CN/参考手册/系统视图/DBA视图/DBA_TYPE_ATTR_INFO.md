本视图显示所有自定义类型（UDT）中OBJECT子类型的内部属性信息。

|  字段| 类型| 说明|
|------------|-------------|----------|
| OWNER      | VARCHAR(64) | UDT所属用户名 |
| TYPE_NAME  | VARCHAR(64) | UDT的名称   |
| TOID       | BIGINT      | UDT的TOID |
| TYPECODE   | TINYINT     | UDT子类的类型 |
| ATTR_NAME  | VARCHAR(68) | 属性的名称    |
| ATTR_NO    | INTEGER     | 属性的编号    |
| ATTR_TOID  | BIGINT      | 属性的TOID  |
| ATTR_TYPE  | VARCHAR(64) | 属性的类型    |
| ATTR_OWNER | VARCHAR(64) | 属性所属用户名  |
