本视图显示主备复制汇总信息。

|  字段| 类型| 说明|
| ------------ |---------------| ------------------------------------ |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID       | NUMBER        | 实例ID |
| MESSAGE\_NUM | INTEGER       | 信息序号                             |
| TYPE         | VARCHAR(32)   | 信息的类型                           |
| SEVERITY     | VARCHAR(16)   | 信息的严重性                         |
| DEST\_ID     | TINYINT       | 备库ID，与ARCHIVE\_DEST\_x参数相对应 |
| ERROR\_CODE  | INTEGER       | 错误码序号                           |
| FIRST\_TIME | DATE          | 首次出现的时间 |
| LAST\_TIME | DATE          | 最后一次出现的时间 |
| REPEAT\_TIME | BIGINT        | 重复出现的次数 |
| MESSAGE | VARCHAR(8192) | 信息内容 |
