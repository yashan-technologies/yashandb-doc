本视图显示所有自定义类型（UDT）信息。

| 字段  | 类型  | 说明                                            |
| --- | --- |-----------------------------------------------|
| OWNER | VARCHAR(64) | UDT所属用户名                                      |
| TYPE_NAME | VARCHAR(64) | UDT的名称                                        |
| TYPE_OID | BIGINT | UDT的标识号                                       |
| TYPECODE | VARCHAR(64) | UDT的类型<br>  * OBJECT：对象 <br>  * COLLECTION：集合 |
| ATTRIBUTES | INTEGER | OBJECT的属性个数                                   |
| METHODS | INTEGER | OBJECT的方法个数                                   |
| PREDEFINED | VARCHAR(3) | UDT是否是预定义类型                                   |
| INCOMPLETE | VARCHAR(3) | UDT是否是不完整的                                    |
| FINAL | VARCHAR(3) | UDT是否是最终类型                                    |
| INSTANTIABLE | VARCHAR(3) | UDT是否是可实例化的                                   |
| PERSISTABLE | VARCHAR(3) | UDT是否是持久的                                     |
| SUPERTYPE_OWNER | VARCHAR(64) | 超类型的用户名。非子类型时为NULL                            |
| SUPERTYPE_NAME | VARCHAR(64) | 超类型的名称。非子类型时为NULL                             |
| LOCAL_ATTRIBUTES | INTEGER | OBJECT的非继承属性的个数                               |
| LOCAL_METHODS | INTEGER | OBJECT的非继承方法的个数                               |
| TYPEID | BIGINT | UDT的类型ID                                      |