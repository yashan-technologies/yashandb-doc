本视图显示所有本地稳态数据同步情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| DEST_ID  | INTEGER | 备库ID，与ARCHIVE_DEST_x参数相对应 |
| BUCKET_NAME | VARCHAR(255) | 同步数据所在databucket路径 |
| DATAOBJ | BIGINT | 同步数据所属对象ID |
| SLICE\_ID  | BIGINT |	同步数据文件编号 |
| PROGRESS | INTEGER |同步进度，范围[0,100] |
| SPEED | INTEGER | 同步速度（单位：MB/s） |
| EXEC\_ROUND | INTEGER | 当前数据已尝试同步次数 |
