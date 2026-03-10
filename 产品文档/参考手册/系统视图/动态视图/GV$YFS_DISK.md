本视图显示 YFS 磁盘（Disk）信息。

| 字段               | 类型           | 说明                                                                        |
|------------------|--------------|---------------------------------------------------------------------------|
| GROUP_ID         | NUMBER       | 组ID                                                                       |
| GROUP_NODE_ID    | NUMBER       | 组内节点ID                                                                    |
| INST_ID          | NUMBER       | 实例ID                                                                      |
| ID               | INTEGER      | disk在所属Diskgroup内的ID                                                              |
| GLOBAL_ID        | INTEGER      | disk的全局ID                                                                      |
| NAME             | VARCHAR(32)  | 名称                                                                        |
| GROUP_NUMBER     | INTEGER      | 所属Diskgroup的ID                                                            |
| FAILGROUP_ID     | INTEGER      | 所属Failgroup的ID                                                            |
| FAILGROUP_LABEL  | VARCHAR(32)  | 所属Failgroup名称                                                             |
| MOUNT_STATUS     | VARCHAR(16)  | 状态<br>\* NORMAL：所属 Diskgroup MOUNTED<br>\* CLOSED：所属 Diskgroup DISMOUNTED |
| REDUNDANCY       | VARCHAR(16)  | 冗余度<br>\* EXTERNAL<br>\* NORMAL<br>\* HIGH                                |
| PARTNERS         | VARCHAR(161) | 伙伴磁盘的全局ID清单，以空格分隔                                                         |
| TOTAL_MB         | BIGINT       | 总空间大小                                                                     |
| FREE_MB          | BIGINT       | 空闲空间大小                                                                    |
| PATH             | VARCHAR(32)  | 块设备路径                                                                     |
| READS            | BIGINT       | 读请求数                                                                      |
| WRITES           | BIGINT       | 写请求数                                                                      |
| BYTES_READ       | BIGINT       | 读字节数                                                                      |
| BYTES_WRITTEN    | BIGINT       | 写字节数                                                                      |
| READ_TIME        | BIGINT       | 读耗时                                                                       |
| WRITE_TIME       | BIGINT       | 写耗时                                                                       |