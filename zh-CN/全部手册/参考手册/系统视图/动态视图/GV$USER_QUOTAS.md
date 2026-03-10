本视图显示所有的用户对应的表空间配额，以及配额的使用情况，在表空间没有设置过配额时，该表空间的相关信息不展示。

|  字段| 类型| 说明|
| --- |-------------|-------------------------------------------------------------------------------------|
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| TABLESPACE_NAME | VARCHAR(64) | 表空间名字                                                                              |
| USERNAME | VARCHAR(64) | 用户名                                                                              |
| BYTES | BIGINT | 对应表空间的已使用字节数                                                                              |
| MAX_BYTES | BIGINT | 最多可用的字节数                                                                              |
| MIN_BYTES | BIGINT | 最少可用的字节数，即表空间需要为该用户预留的空间                                                                        |
| BLOCKS | BIGINT | 对应表空间的已使用blocks数                                                                        |
| MAX_BLOCKS | BIGINT | 用户在该表空间最多可使用blocks数                                                                        |
| MIN_BLOCKS | BIGINT | 用户在该表空间最少可使用blocks数                                                                        |
