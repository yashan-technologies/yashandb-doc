本视图显示上次统计信息收集之后，内存中记录的表变化的情况，用于内部机制的实现。用户如果需要查看表的变化情况，请使用USER_TAB_MODIFICATIONS视图。

|  字段| 类型| 说明|
| --- | --- | --- |
| OBJ# | BIGINT | 对象ID |
| BASE_OBJ# | BIGINT | 父对象ID |
| INSERTS | BIGINT | 插入的行数 |
| UPDATES | BIGINT | 更新的行数 |
| DELETES | BIGINT | 删除的行数 |
| CURR_ROWS | BIGINT | 上次统计信息收集获取的行数 |
| LAST_MODIFY | DATE | 上次变更的时间 |
| FLAGS | INTEGER | 标志字段，标识是否被truncate |
| DROP_SEGS | BIGINT | segment被truncate的次数 |
