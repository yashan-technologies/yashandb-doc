本视图显示所有表空间集信息，仅适用于存算一体分布式集群部署。

|  字段| 类型| 说明|
|-------------------------|--------------|------------------------------------------------------|
| GROUP_ID                | NUMBER       | 组ID                                                  |
| GROUP_NODE_ID           | NUMBER       | 组内节点ID                                               |
| INST_ID                 | NUMBER       | 实例ID                                                 |
| OID                     | BIGINT       | 表空间集的oid                                         |
| NAME                    | VARCHAR(64)  | 表空间集的名称                                        |
| DS_ID                   | BIGINT       | 所属dataspace的id                                     |
|NEXT_BLOCKS              | INTEGER      | 该表空间集中的数据文件单次扩张的数据块数量 |
|BLOCK_SIZE               | INTEGER      | 数据文件的数据块大小（单位：字节）|
|DATAFILES_COUNT          | NUMBER(38)       | 该表空间集中数据文件的总数|
|DATAFILES_MAX_SIZE       | NUMBER       | 该表空间集在当前节点的数据文件总的最大SIZE|
|DATAFILES_SIZE           | NUMBER       | 该表空间集中所有datafile的实际大小的总和|
|DATAFILES_FREE_BLOCKS    | NUMBER       | 该表空间集中所有datafile的空闲可用数据块总数|
|DATABUCKETS_COUNT        | NUMBER(38)       | 该表空间集中DATABUCKET文件的总数|
|DATABUCKETS_MAX_SIZE     |NUMBER        | 该表空间集在当前节点的所有DATABUCKET文件的最大SIZE总和|
|DATABUCKETS_SIZE         |NUMBER        | 该表空间集中所有databucket的实际大小的总和|
