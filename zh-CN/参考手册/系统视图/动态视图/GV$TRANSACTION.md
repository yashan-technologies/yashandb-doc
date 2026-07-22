本视图显示事务汇总信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| XID | BIGINT | 事务的全局ID |
| SID | INTEGER | 该事务绑定的会话ID |
| XRMID | INTEGER | 该事务的XRM ID |
| XEXT | INTEGER | 当前事务对应的事务区编号 |
| XNODE | INTEGER | 当前事务在对应事务区内的编号 |
| XSN | INTEGER | 当前事务的版本号 |
| STATUS | VARCHAR(8) | 事务状态<br>\*   IDLE<br>\*   OPEN<br>\*   PHASE1<br>\*   END |
| RESIDUAL | VARCHAR(8) | 事务是否在后台线程等待回滚<br>\*   TRUE<br>\*   FALSE |
| USED\_UBLK | INTEGER | 事务使用的undo block数量 |
| FIRST\_UBAFIL | INTEGER | 事务使用的第一个undo block文件号 |
| FIRST\_UBABLK | INTEGER | 事务使用的第一个undo block的ID |
| FIRST\_UBAVER | INTEGER | 事务使用的第一个undo block版本号 |
| FIRST\_UBAREC | INTEGER | 事务开启后第一条undo记录的编号 |
| LAST\_UBAFIL | INTEGER | 事务的最后一个undo block文件号 |
| LAST\_UBABLK | INTEGER | 事务最后一个undo block的ID |
| PRV_XID | BIGINT | 前事务ID，正常为空<br>若本事务为自治事务时，该字段为前一个事务ID |
| PTX_XID | BIGINT | 父事务ID，当前事务非并行事务时为空<br>若当前事务是并行事务：<br>\*   若当前事务是父事务，该字段为当前事务ID<br>\*   若当前事务是子事务，该字段为父事务的ID |
| START\_DATE | DATE | 事务启动时间 |
| ISOLATION\_LEVEL | VARCHAR(32) | 隔离级别 |
| START\_SCN | BIGINT | 事务启动时的scn |
