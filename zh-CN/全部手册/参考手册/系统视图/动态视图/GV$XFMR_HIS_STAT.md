本视图显示列式存储用于转换生成稳态数据的所有已经执行完毕的xfmr任务信息。

|  字段| 类型| 说明|
|------------------|---------------|------------------------------------------------------------------------------------------------------|
| GROUP_ID         | NUMBER        | 组ID                                                                                                  |
| GROUP_NODE_ID    | NUMBER        | 组内节点ID                                                                                               |
| INST_ID          | NUMBER        | 实例ID                                                                                                 |
| ID               | BIGINT        | xfmr任务编号                                                                                             |
| OWNER            | VARCHAR(64)   | xfmr任务所属用户名                                                                                          |
| TABLESPACE_NAME | VARCHAR(64)   | xfmr任务所属的表空间名                                                                                        |
| TABLE_NAME      | VARCHAR(64)   | xfmr任务所属的表名                                                                                          |
| PARTITION_NAME  | VARCHAR(64)   | xfmr任务所属的分区名                                                                                         |
| TYPE             | VARCHAR(16)   | xfmr任务的类型，包括transform、compact等                                                                       |
| FORCE            | VARCHAR(16)   | xfmr任务是否为强制执行                                                                                        |
| STATUS           | VARCHAR(16)   | xfmr任务的状态：<br/> * finished：已经执行完成                                                                    |
| SLICE_INFO      | VARCHAR(1024) | xfmr任务对应的slice信息：<br/> * 转换任务为从需要转换的slice到生成slice的对应id和行数<br/> * 合并任务为从需要合并的所有slice到生成slice的对应id和行数  |
| FILE_ID         | BIGINT        | xfmr任务生成slice的对应文件id                                                                                 |
| CREATE_TIME     | TIMESTAMP     | xfmr任务的创建时间                                                                                          |
| START_TIME      | TIMESTAMP     | xfmr任务的开始执行时间                                                                                        |
| FINISH_TIME     | TIMESTAMP     | xfmr任务的结束执行时间                                                                                        |
| EXECUTE_COUNT   | INTEGER       | xfmr任务的执行次数                                                                                          |
| MAX_MEM_USE    | BIGINT        | xfmr任务执行中的最大内存大小                                                                                     |
| MAX_MEM_QUOTA  | BIGINT        | xfmr任务执行中的最大内存配额大小                                                                                   |
| SWAP_OUT_SIZE  | BIGINT        | xfmr任务当前执行中换出的字节大小                                                                                   |
| SWAP_IN_SIZE   | BIGINT        | xfmr任务当前执行中换入的字节大小                                                                                   |
| SWAP_OUT_COUNT | INTEGER       | xfmr任务当前执行中换出的次数                                                                                     |
| SWAP_IN_COUNT  | INTEGER       | xfmr任务当前执行中换入的次数                                                                                     |
| SWAP_OUT_TIME  | BIGINT        | xfmr任务当前执行中换出的时间                                                                                     |
| SWAP_IN_TIME   | BIGINT        | xfmr任务当前执行中换入的时间                                                                                     |
