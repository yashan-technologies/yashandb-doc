本视图显示所有自定义类型（UDT）中OBJECT类型的方法信息。

| 字段  | 类型  | 说明                                                          |
| --- | --- |-------------------------------------------------------------|
| OWNER | VARCHAR(64) | UDT所属用户名                                                    |
| TYPE_NAME | VARCHAR(64) | UDT的名称                                                      |
| METHOD_NAME | VARCHAR(68) | 方法的名称                                                       |
| METHOD_NO | INTEGER | 方法的序号。从1开始                                                  |
| METHOD_TYPE | VARCHAR(6) | 方法的类型<br>  * MAP：映射函数 <br>  * ORDER：排序函数<br>  * PUBLIC：公共函数 |
| PARAMETERS | INTEGER | 方法的参数个数                                                     |
| RESULTS | INTEGER | 方法的返回值个数                                                    |
| FINAL | VARCHAR(3) | 方法是否是最终的                                                    |
| INSTANTIABLE | VARCHAR(3) | 方法是否是可实例化的                                                  |
| OVERRIDING | VARCHAR(3) | 方法是否是重写的                                                    |
| INHERITED | VARCHAR(3) | 方法是否是继承的                                                    |
