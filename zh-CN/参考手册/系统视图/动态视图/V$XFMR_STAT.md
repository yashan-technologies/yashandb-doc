本视图显示列式存储用于转换生成稳态数据的所有还未执行完毕的xfmr任务信息。

|  字段| 类型| 说明|
|------------------|---------------|------------------------------------------------------------------------------------------------------|
| ID               | BIGINT        | xfmr任务编号                                                                                             |
| OWNER            | VARCHAR(64)   | xfmr任务所属用户名                                                                                          |
| TABLESPACE\_NAME | VARCHAR(64)   | xfmr任务所属的表空间名                                                                                        |
| TABLE\_NAME      | VARCHAR(64)   | xfmr任务所属的表名                                                                                          |
| PARTITION\_NAME  | VARCHAR(64)   | xfmr任务所属的分区名                                                                                         |
| TYPE             | VARCHAR(16)   | xfmr任务的类型，包括ac、clean、create、transform、compact等                                                       |
| FORCE            | VARCHAR(16)   | xfmr任务是否为强制执行                                                                                        |
| STATUS           | VARCHAR(16)   | xfmr任务的状态：<br/> * ready：已创建等待执行中<br/> * running：正在执行中                                                |
| SLICE\_INFO      | VARCHAR(1024) | xfmr任务对应的slice信息：<br/> * 转换任务为从需要转换的slice到生成slice的对应id和行数<br/> * 合并任务为从需要合并的所有slice到生成slice的对应id和行数  |
| CREATE\_TIME     | TIMESTAMP(6)     | xfmr任务的创建时间                                                                                          |
| START\_TIME      | TIMESTAMP(6)     | xfmr任务的开始执行时间                                                                                        |
| EXECUTE\_COUNT   | INTEGER       | xfmr任务的执行次数                                                                                          |
| ERROR\_CODE      | INTEGER       | xfmr任务当前的失败错误码                                                                                       |
| ERROR\_MESSAGE   | VARCHAR(512)  | xfmr任务当前的失败错误信息                                                                                      |
| MEM\_USE         | BIGINT        | xfmr任务当前已使用的内存大小                                                                                     |
| MEM\_QUOTA       | BIGINT        | xfmr任务当前的内存配额大小                                                                                      |
| SWAP\_OUT\_SIZE  | BIGINT        | xfmr任务当前执行中换出的字节大小                                                                                   |
| SWAP\_IN\_SIZE   | BIGINT        | xfmr任务当前执行中换入的字节大小                                                                                   |
| SWAP\_OUT\_COUNT | INTEGER       | xfmr任务当前执行中换出的次数                                                                                     |
| SWAP\_IN\_COUNT  | INTEGER       | xfmr任务当前执行中换入的次数                                                                                     |
| SWAP\_OUT\_TIME  | BIGINT        | xfmr任务当前执行中换出的时间                                                                                     |
| SWAP\_IN\_TIME   | BIGINT        | xfmr任务当前执行中换入的时间                                                                                     |
