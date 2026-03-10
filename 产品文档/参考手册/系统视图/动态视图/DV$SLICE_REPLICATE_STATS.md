分布式部署中，除CN外，本视图显示当前实例的所有本地稳态数据同步情况；在CN上，本视图显示MN实例的所有本地稳态数据同步情况。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| DEST_ID  | INTEGER | 备库ID，与ARCHIVE_DEST_x参数相对应 |
| BUCKET_NAME | VARCHAR(255) | 同步数据所在databucket路径 |
| DATAOBJ | BIGINT | 同步数据所属对象ID |
| SLICE_ID  | BIGINT |	同步数据文件编号 |
| PROGRESS | INTEGER |同步进度，范围[0,100] |
| SPEED | INTEGER | 同步速度（单位：MB/s） |
| EXEC_ROUND | INTEGER | 当前数据已尝试同步次数 |