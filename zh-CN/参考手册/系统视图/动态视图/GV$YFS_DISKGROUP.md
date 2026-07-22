本视图显示YFS中的磁盘组信息。

|  字段| 类型| 说明|
|----------------|-------------|------------------------------------------------|
| GROUP_ID       | NUMBER      | 组ID                                            |
| GROUP_NODE_ID  | NUMBER      | 组内节点ID                                         |
| INST_ID        | NUMBER      | 实例ID                                           |
|  ID|INTEGER| 磁盘组的ID|
|NAME|VARCHAR(32)|磁盘组的名称|
|TYPE|VARCHAR(32)|磁盘组的类型<br>\* SYSTEM：系统磁盘组，用于存储YFS自身元数据<br>\* USER：数据磁盘组，用于存储业务数据文件 |                   
|AU_SIZE|INTEGER|分配单元的大小（单位为字节），是YFS分配磁盘空间的最小维度 |
|BLOCK_SIZE|INTEGER|文件数据块的大小（单位为字节） |
|REDUNDANCY|VARCHAR(16)|磁盘组的冗余度<br>\* External：YFS不提供数据冗余副本 <br>\* Normal：系统磁盘组在该配置下提供[2,3]份副本（具体份数取决于故障组数量）；数据盘组在该配置下提供[2,3]份YFS元数据副本（具体份数取决于故障组数量）+2份用户数据副本  <br>\* High：系统磁盘组在该配置下提供5份副本；数据盘组在该配置下提供[3,5]份YFS元数据副本（具体份数取决于故障组数量）+3份用户数据副本 |
|STATE| VARCHAR(16) | 磁盘组的状态<br>\* BROKEN：磁盘组故障<br>\* MOUNTED：磁盘组状态正常<br>\* DISMOUNTED：磁盘组未挂载 |
|TOTAL_MB | BIGINT    | 磁盘组的总容量（单位为MB） |
|FREE_MB|BIGINT | 磁盘组的可用空间（单位为MB） |
|USABLE_FILE_MB|BIGINT  |磁盘组的可用文件大小（单位为MB），即磁盘组的可用空间扣除多副本所需容量后的值  |
