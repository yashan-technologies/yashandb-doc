本视图显示归档GAP区间。

当备库从异常或非工作状态恢复时会直接从主库最新的redo日志开始接收并回放，异常或非工作状态期间，如果主库产生了redo日志将无法正常发送给备库，备库恢复后其redo日志文件或归档日志文件可能会出现不连续的空洞，此空洞称为GAP。归档GAP需通过备库启动FAL线程从主库获取GAP对应的归档日志文件进行修复。


| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID  | INTEGER | 归档GAP区间ID，如果GAP是多个不连续的区间，将输出至少2行 |
| LOW_SEQUENCE# | INTEGER | 当前归档GAP区间的第一个序号（ASN） |
| HIGH_SEQUENCE# | INTEGER | 当前归档GAP区间的最后一个序号（ASN） |