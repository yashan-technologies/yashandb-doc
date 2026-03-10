本视图显示所有归属于表空间集的表空间，仅适用于分布式部署。

| 字段  | 类型          | 说明                                                                                  |
| --- |-------------|-------------------------------------------------------------------------------------|
| TABLESPACE_NAME | VARCHAR(64)   | 表空间名称                                              |
| TABLESPACE_ID         | INTEGER   | 表空间ID                                                    |
| TABLESPACE_SET_NAME            | VARCHAR(64)       | 所属的表空间集名称                                                |
| CHUNK_ID          | INTEGER       | 表空间对应的chunk id                                      |
| RESIDUAL         | VARCHAR(8)       | 是否为重分布残留的旧表空间，若为TRUE表示是重分布的旧表空间（即当前表空间已迁移至其他节点）        |