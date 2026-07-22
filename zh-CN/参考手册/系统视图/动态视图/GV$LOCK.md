本视图显示当前所有锁的信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST\_ID | NUMBER  | 实例ID |
| SID | SMALLINT | 会话ID |
| ID1 | BIGINT | 锁的标识符<br/>1. 如果是会话持有的锁<br/>* 当锁类别为表锁，ID1记录表的ID<br/>* 当锁类别为行表行锁/键值锁/列表行锁， ID1记录行/索引键/列表中的行所在的页面ID<br/>2. 如果是会话等待的锁<br/>* 当等待的锁类别为表锁，ID1记录表的ID<br/>* 当等待的锁类别为行锁/键值锁/列表行锁，ID1记录持有该行锁/键值锁/列表行锁的事务ID |
| ID2 | BIGINT | 锁的标识符<br/>*   如果是会话持有的行锁，ID2记录该行所对应的Xslot ID<br/>*   其他情况下ID2为空 |
| LMODE | VARCHAR(32) | 会话持有的锁类型<br/>*   TS：共享表锁<br/>*   TX：排他表锁<br/>*   ROW：行锁<br/>*   KEY：键值锁<br/>*   SLICE\_S：LSC表slice共享锁<br/>*   SLICE\_X：LSC表slice排他锁 |
| REQUEST | VARCHAR(32) | 会话等待的锁类型<br/>*   TS：共享表锁<br/>*   TX：排他表锁<br/>*   ROW：行锁<br/>*   KEY：键值锁<br/>*   SLICE\_S：LSC表slice共享锁<br/>*   SLICE\_X：LSC表slice排他锁 |
