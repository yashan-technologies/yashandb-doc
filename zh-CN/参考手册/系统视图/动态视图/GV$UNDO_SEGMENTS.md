本视图用于展示数据库中当前所有undo segment里的undo block使用情况。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| ID  | BIGINT | segment ID |
| USED\_TIME | DATE | undo segment上被已提交事务归还的block中，第一个undo block所在的事务提交的时间 |
| SURPLUS\_COUNT | INTEGER | 在保持undo链自动均衡优化的情况下多余的block数量 |
| UBLK\_COUNT | INTEGER | 已提交事务归还的undo block数量 |
| FIRST\_UBAFIL | INTEGER | 已提交事务归还的第一个undo block的文件编号 |
| FIRST\_UBABLK | INTEGER | 已提交事务归还的第一个undo block的ID |
| LAST\_UBAFIL | INTEGER | 已提交事务归还的最后一个undo block的文件编号 |
| LAST\_UBABLK | INTEGER | 已提交事务归还的最后一个undo block的ID |
| UFB\_COUNT | INTEGER | 未初始化的undo block数量 |
| FIRST\_UFBFIL | INTEGER | 第一个未初始化的undo block的文件编号 |
| FIRST\_UFBBLK | INTEGER | 第一个未初始化的undo block的ID |
| XBLKS | INTEGER | undo segment管理的事务block数量（保留字段） |
| FREE\_COUNT | INTEGER | 已提交事务归还的可以直接复用的undo block的数量 |
