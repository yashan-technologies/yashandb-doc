本视图显示所有自定义类型(UDT)中组合类型的内部详细信息。

|  字段| 类型| 说明|
|------------------|-------------|----------|
| OWNER            | VARCHAR(64) | UDT所属用户名 |
| NAME             | VARCHAR(64) | UDT的名称   |
| TOID             | BIGINT      | UDT的TOID |
| TYPECODE         | TINYINT     | UDT子类的类型 |
| COLL_VERSION     | INTEGER     | 属性版本     |
| COLL_TOID        | BIGINT      | 属性的TOID  |
| COLL_UPPER_BOUND | INTEGER     | 属性的上限    |
| COLL_LENGTH      | INTEGER     | 属性的长度    |
| COLL_PRECISION   | INTEGER     | 属性的精度    |
| COLL_SCALE       | INTEGER     | 属性的范围    |
