本视图显示所有自定义类型(UDT)中组合类型的详细信息。

| 字段  | 类型          | 说明                                                    |
| --- |-------------|-------------------------------------------------------|
| OWNER | VARCHAR(64) | UDT所属用户名                                              |
| TYPE_NAME | VARCHAR(64) | UDT的名称                                                |
| COLL_TYPE | VARCHAR(13) | 组合类型的类型<br> * TABLE：嵌套表<br> * VARYING ARRAY：可变长数组<br> |
| UPPER_BOUND | INTEGER     | 数组类型的最大元组个数                                           |
| ELEM_TYPE_MOD | VARCHAR(7)  | 保留                                                    |
| ELEM_TYPE_OWNER | VARCHAR(64) | 元素的所属用户名。内置类型时为NULL。                                  |
| ELEM_TYPE_NAME | VARCHAR(64) | 元素的类型名称。                                              |
| LENGTH | INTEGER     | 元素的长度                                                 |
| PRECISION | INTEGER     | 元素的精度                                                 |
| SCALE | INTEGER     | 元素的范围                                                 |
| CHARACTER_SET_NAME | TINYINT     | 保留                                                    |
| ELEM_STORAGE | VARCHAR(1)  | 保留                                                    |
| NULLS_STORED | VARCHAR(3)  | 是否可以存储NULL值                                           |