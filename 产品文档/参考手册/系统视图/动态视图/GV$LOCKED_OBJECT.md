本视图显示当前所有对象锁的信息。

| 字段       | 类型         | 说明                                                         |
| ---------- |------------| ------------------------------------------------------------ |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| XEXT       | SMALLINT   | 当前事务对应的事务区编号                                     |
| XNODE      | SMALLINT   | 当前事务在对应事务区内的编号                                 |
| XSN        | INTEGER    | 当前事务的版本号                                             |
| OBJECT_ID  | BIGINT     | 表的ID                                                       |
| SESSION_ID | SMALLINT   | 会话ID                                                       |
| LMODE      | VARCHAR(8) | 会话持有的锁类型<br/>*   TS：共享表锁<br/>*   TX：排他表锁<br/>*   NONE：等待表锁 |

