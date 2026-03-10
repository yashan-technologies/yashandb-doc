本视图显示所有自定义类型（UDT）中OBJECT类型的属性信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| OWNER | VARCHAR(64) | UDT所属用户名 |
| TYPE_NAME | VARCHAR(64) | UDT的名称 |
| ATTR_NAME | VARCHAR(68) | 属性的名称 |
| ATTR_TYPE_MOD | VARCHAR(7) | 保留字段，值为NULL |
| ATTR_TYPE_OWNER | VARCHAR(64) | 属性的所属用户名。内置类型时为NULL。 |
| ATTR_TYPE_NAME | VARCHAR(64) | 属性的类型名称 |
| LENGTH | INTEGER | 属性的长度 |
| PRECISION | INTEGER | 属性的精度 |
| SCALE | INTEGER | 属性的范围 |
| CHARACTER_SET_NAME | TINYINT | 保留字段，值为NULL |
| ATTR_NO | INTEGER | 属性的序号。从1开始 |
| INHERITED | VARCHAR(3) | 属性是否是继承的 |
