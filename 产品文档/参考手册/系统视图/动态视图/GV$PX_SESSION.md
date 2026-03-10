本视图显示正在运行并行任务的会话信息。

| 字段                     | 类型         | 说明  |
| ---                     | ---         | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
| SID                     | SMALLINT    | 并行任务的会话ID |
| SERIAL#                 | INTEGER     | 会话的序列号 |
| PADDR                   | BIGINT      | 执行该会话的线程地址，即并行worker的线程地址 |
| QCSID                   | SMALLINT    | 并行协调者的会话ID |
| QCSERIAL#               | INTEGER     | 并行协调者的会话的序列号 |
| PARENT_STAGE_ID       | SMALLINT    | 父stage ID |
| STAGE_ID               | SMALLINT    | stage ID，stage是并行worker执行的最小任务，通常由一个sender，若干个receiver或者表扫描组成 |
| SLICE_ID               | SMALLINT    | 在当前stage中的分片ID，每个stage可以划分为多个逻辑分片，实际执行时的最大分片ID为所分配到的worker数量-1 |
| TQ_ID                   | SMALLINT    | 发送表队列ID，每个stage的入口是并行执行发送端所在的表队列ID |
| DEGREE                  | SMALLINT    | 执行时的并行度，即所分配到的worker数量 |
| REQ_DEGREE             | SMALLINT    | 期望的执行并行度 |