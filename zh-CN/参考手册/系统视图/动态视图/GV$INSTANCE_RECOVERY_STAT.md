本视图显示最近一次实例恢复任务的详细统计信息。

|  字段| 类型| 描述|
| --- | --- | --- |
| GROUP_ID | NUMBER | 组ID |
| GROUP_NODE_ID | NUMBER | 组内节点ID |
| INST_ID | NUMBER  | 实例ID |
|  ITEM | VARCHAR(32) | 统计项的名称 |
| UNITS | VARCHAR(16) | 统计项的单位 |
| VALUE | BIGINT | 统计项的值 |



统计项信息：

| 统计项| 单位| 描述|
| --- | --- | --- |
| Grc Remater Time                 | Millisecond   |  GRC资源重分布的耗时          |
| Grc Recover Time                 | Millisecond   |  GRC资源恢复的耗时            |
| Reform Init Time                 | Millisecond   |  重整初始化相关资源的耗时    |
| Redo Analyze Time                | Millisecond   |  Redo分析的耗时               |
| Recovery Set Blocks              | Number        |  恢复集里的块数量          |
| Replay Blocks                    | Number        |  需要回放的块数量          |
| Current Blocks                   | Number        |  恢复集里有最新版本的块数量 |
| Lock Recovery Set Reads          | Number        |  分析恢复集产生的读次数        |
| Lock Recovery Set Read Time      | Millisecond   |  分析恢复集产生读的耗时        |
| Lock Recovery Set Writes         | Number        |  分析恢复集产生的写次数        |
| Lock Recovery Set Write Time     | Millisecond   |  分析恢复集产生写的耗时        |
| Reform Prepare Time              | Millisecond   |  重整准备prepare阶段的耗时   |
| Alloc Ctrls                      | Number        |  从缓冲区池分配的ctrl数量   |
| Alloc Ctrl Time                  | Millisecond   |  从缓冲区池分配ctrl的耗时   |
| Lock Recovery Set Time           | Millisecond   |  恢复集分析的耗时              |
| Lock Recovery Set Threads        | Threads       |  恢复集分析的并行线程数        |
| Physical Recover Reads           | Number        |  物理回放时产生的读次数        |
| Physical Recover Read Time       | Millisecond   |  物理回放时产生读的耗时        |
| Physical Recover Writes          | Number        |  物理回放时产生的写次数        |
| Physical Recover Write Time      | Millisecond   |  物理回放时产生写的耗时        |
| Physical Recover Time            | Millisecond   |  物理回放的耗时               |
| Physical Recover Threads         | Threads       |  物理回放的并行线程数          |
| Physical Recover Write Threads   | Threads       |  物理回放刷脏页的并行线程数    |
| Flush Current Time               | Millisecond   |  刷最新版本块的耗时        |
| Flush Current Threads            | Threads       |  刷最新版本的并行线程数       |
