本视图显示 YFS 文件（File）信息。

| 字段             | 类型          | 说明                                           |
|----------------|-------------|----------------------------------------------|
| GROUP_ID       | NUMBER      | 组ID                                          |
| GROUP_NODE_ID  | NUMBER      | 组内节点ID                                       |
| INST_ID        | NUMBER      | 实例ID                                         |
| FILE_NUMBER    | INTEGER     | DiskGroup内文件编号                               |
| COMPOUND_INDEX | INTEGER     | 含Diskgroup ID的文件句柄                           |
| GROUP_NUMBER   | INTEGER     | 所属Diskgroup的ID                               |
| BLOCK_SIZE     | INTEGER     | 文件块大小，即所在Diskgroup的AU Size                   |
| BLOCKS         | BIGINT      | 文件的Block数量                                   |
| BYTES          | BIGINT      | 文件字节数                                        |
| REDUNDANCY     | VARCHAR(16) | 文件冗余度<br/>* EXTERNAL：没有镜像<br/>* NORMAL：有两份镜像<br/>* HIGH：有三份镜像|
| CREATION_DATE  | DATE        | 创建日期                                         |
| DELETE_TIME    | DATE        | 删除日期                                         |
| TYPE           | INTEGER     | 文件类型编号，200以上为元数据类型，类型编号由DB指定                 |