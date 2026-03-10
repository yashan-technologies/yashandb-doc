本视图显示Database Writer线程的统计信息。

| 字段  | 类型  | 说明  |
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| ID | INTEGER | Database Writer的ID |
| CURRENT_STATUS | VARCHAR(32) | 线程当前状态<br>\* CHECKPOINT：检查点触发刷盘<br>\* BUFFER CLEAN：脏页淘汰触发刷盘<br>\* CURRENT CLEAN：pastcopy触发刷盘<br>\* OBJECT CLEAN：drop对象触发刷盘<br>\* IDLE：空闲 |
| CAPACITY | INTEGER | Database Writer的最大block容量 |
| BLOCK_NUM |  INTEGER | 当前刷盘的block数量|
| BUFFER_CLEAN_NUM | BIGINT | 执行buffer clean的次数 |
| CHECKPOINT_NUM |  BIGINT | 执行checkpoint的次数 |
| PINNED_BLOCKS |  BIGINT | prepare blocks时，遇到被pinned的block数量 |