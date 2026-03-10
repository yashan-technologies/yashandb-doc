本视图显示分布式集群中所有节点的主备复制汇总信息。

|  字段| 类型| 说明|
| --- |---------------| --- |
| GROUP_ID | INTEGER       | 组ID |
| GROUP_NODE_ID | INTEGER       | 组内节点ID |
| MESSAGE_NUM | INTEGER       | 信息序号 |
| TYPE | VARCHAR(32)   | 信息的类型 |
| SEVERITY | VARCHAR(16)   | 信息的严重性 |
| DEST_ID | TINYINT       | 备库ID，与ARCHIVE_DEST_x参数相对应 |
| ERROR_CODE | INTEGER       | 错误码序号 |
| FIRST_TIME | DATE          | 首次出现的时间 |
| LAST_TIME | DATE          | 最后一次出现的时间 |
| REPEAT_TIME | BIGINT        | 重复出现的次数 |
| MESSAGE | VARCHAR(8192) | 信息内容 |
