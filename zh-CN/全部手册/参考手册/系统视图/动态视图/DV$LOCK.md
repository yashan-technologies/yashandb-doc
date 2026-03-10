本视图显示存算一体分布式集群中所有节点的锁信息。

|  字段| 类型| 说明|
| --- | --- | --- |
| GROUP_ID | INTEGER | 组ID |
| GROUP_NODE_ID | INTEGER | 组内节点ID |
| SID | SMALLINT | 会话ID |
| ID1 | BIGINT | 锁的标识符<br>1.如果是会话持有的锁<br>当锁类别为表锁，ID1记录了表的ID<br>当锁类别为行表行锁/键值锁/列表行锁， ID1记录了行/索引键/列表中的行所在的页面ID<br>2.如果是会话等待的锁<br>当等待的锁类别为表锁，ID1记录了表的ID<br>当等待的锁类别为行锁/键值锁/列表行锁， ID1记录了持有该行锁/键值锁/列表行锁的事务ID |
| ID2 | BIGINT | 锁的标识符<br>\*   如果是会话持有的行锁，ID2记录了该行所对应的Xslot ID<br>\*   其他情况下ID2为空 |
| LMODE | VARCHAR(32) | 会话持有的锁类型<br>\*   TS：共享表锁<br>\*   TX：排他表锁<br>\*   ROW：行锁<br>\*   KEY：键值锁<br>\*   SLICE_S：LSC表slice共享锁<br>\*   SLICE_X：LSC表slice排他锁 |
| REQUEST | VARCHAR(32) | 会话等待的锁类型<br>\*   TS：共享表锁<br>\*   TX：排他表锁<br>\*   ROW：行锁<br>\*   KEY：键值锁<br>\*   SLICE_S：LSC表slice共享锁<br>\*   SLICE_X：LSC表slice排他锁 |
