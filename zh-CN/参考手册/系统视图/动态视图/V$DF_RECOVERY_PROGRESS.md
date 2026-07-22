本视图显示表空间文件回放进度汇总信息。

仅适用于单机部署，其他部署形态中本视图无意义。

|  字段| 类型| 说明|
| --- | --- | --- |
| RCY_START  | INTEGER  | 回放开始日志的ASN |
| RCY_START_LFN  | BIGINT | 回放开始日志的LFN |
| RCY_END  | INTEGER | 回放结束日志的ASN |
| RCY_END_LFN  | BIGINT | 回放结束日志的LFN |
| REPLAY_POINT  | INTEGER | 当前回放日志的ASN |
| REPLAY_POINT_LFN  | BIGINT | 当前回放日志的LFN | 
