本视图显示所有在库缓存中的绑定变量的相关信息。

|  字段| 类型| 说明|
|-----------------|---------------|---------------------------------------------|
| GROUP_ID        | NUMBER        | 组ID                                         |
| GROUP_NODE_ID   | NUMBER        | 组内节点ID                                      |
| INST_ID         | NUMBER        | 实例ID                                        |
| ADDRESS         | RAW(8)        | SQL地址                                       |
| HASH\_VALUE     | BIGINT        | SQL的哈希值，由SQL文本计算得到                          |
| SQL\_ID         | VARCHAR(13)   | 唯一标识一条SQL语句的ID值，具体算法通过SQL文本的哈希/加密运算获得       |
| CHILD\_ADDRESS  | RAW(8)        | 子游标地址                                       |
| CHILD\_NUMBER   | INTEGER       | 子游标编号                                       |
| NAME            | VARCHAR(64)   | 绑定变量的名称（保留字段）                                |
| POSITION        | INTEGER       | 绑定变量在SQL中的位置                                |
| DUP\_POSITION   | INTEGER       | 如该绑定变量在sql中有重复使用，则此列的值设置为首个扫描到的绑定变量的位置（保留字段） |
| DATATYPE        | INTEGER       | 绑定变量数据类型的内部标识符                              |
| DATATYPE_STRING | VARCHAR(32)   | 绑定变量数据类型的文本表示                               |
| CHARACTER_SID   | INTEGER       | 国家/地区字符集标识符（保留字段）                            |
| PRECISION       | INTEGER       | 绑定变量的精度（保留字段）                                |
| SCALE           | INTEGER       | 绑定变量的范围（保留字段）                                |
| MAX\_LENGTH     | INTEGER       | 绑定变量的最大长度                                   |
| WAS\_CAPTURED   | VARCHAR(3)    | 表示绑定变量的值是否被捕获（保留字段）                          |
| LAST\_CAPTURED  | DATE          | 最近一次捕获绑定变量的时间（保留字段）                          |
| VALUE\_STRING   | VARCHAR(4000) | 绑定变量的值，使用字符串表示（保留字段）                         |
