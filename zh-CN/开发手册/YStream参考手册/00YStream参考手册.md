YStream是YashanDB提供的逻辑日志解析接口，支持Java语言的客户端API。

用户可以在其他软件中集成YStream API的jar包，调用API接口进行数据变更捕获（CDC，Change Data Capture）、获取并解析YashanDB的附加日志，包括DML和DDL。

存算一体分布式集群部署中无YStream功能。

## 准备工作

### 主备环境要求

在单机主备部署、主备集群部署中，调用[DBMS_YSTREAM_ADM](../PL参考手册/内置高级包/DBMS_YSTREAM_ADM.md)高级包管理YStream服务时，对操作者存在以下约束：

- YStream服务的创建（DBMS_YSTREAM_ADM.CREATE）、表对象配置（DBMS_YSTREAM_ADM.ADD_TABLES和DBMS_YSTREAM_ADM.DROP_TABLES）、参数配置（DBMS_YSTREAM_ADM.SET_PARAMETER）以及删除（DBMS_YSTREAM_ADM.DROP）均只能在主库/主集群上执行。

- 通常情况下，启（DBMS_YSTREAM_ADM.START）/停（DBMS_YSTREAM_ADM.STOP）YStream服务时应遵循“哪用哪开、哪开哪关”原则：

    - 在共享集群/分布式集群部署中，同一YStream服务的启停通常要求在同一个实例上操作，除非对应实例不可用（例如已离线）方才允许在其他实例上停止该服务。

    - 主库/主集群始终保有关闭备库/备集群上启动的YStream服务的能力，但始终不允许在备集群实例停止主集群实例启动的YStream服务。

在备库/备集群上运行YStream服务时，还需额外注意：

- 应确保主备库/主备集群间网络连接快速、稳定，否则可能出现网络连接断开异常。

- 应确保备库/备集群的计算、存储性能不低于主库/主集群，否则主备日志差距过大可能引起超时异常。

- 主备发生切换时，备库/备集群上运行的YStream服务也可能出现网络断连异常。如需手动执行计划内切换（Switchover）或故障切换（Failover），建议停止YStream服务后再操作。

- 主库/主集群关闭或因其他原因异常退出时，备库/备集群上运行的YStream服务也可能出现网络断连异常。如需手动关闭主库/主集群，建议停止YStream服务后再操作。

### 数据库归档管理

YStream仅支持解析归档模式下生成的redo和归档日志，使用YStream时，必须打开数据库[归档](../../数据库管理/实例管理/归档管理.md)，否则创建YStream服务时报错。

### 数据库附加日志管理

创建YStream服务前，建议先开启附加日志（[库级](../SQL参考手册/SQL语句/ALTER DATABASE.md#supplementallogclauses)或[表级](../SQL参考手册/SQL语句/ANALYZE TABLE.md#addsupplementalloggingclause)）。若不开启则只能解析已有日志中的附加信息，且在已有YStream服务后再开启附加日志会报错。

### 配置数据库YStream服务

配置数据库YStream服务的操作均需以具备YSTREAM_CAPTURE角色的用户调用[DBMS_YSTREAM_ADM](../PL参考手册/内置高级包/DBMS_YSTREAM_ADM.md)高级包完成，具体操作流程可参考[YStream使用示例](./YStream使用示例.md)。

- 创建YStream服务前，必须先按需合理配置STREAM_POOL_SIZE值。

- 请在临使用前再创建YStream服务，并在无需再用后及时删除：

    - YStream服务数量存在上限，应尽量减少无意义的占用。

        - 单机部署中，每个实例最多创建32个YStream服务。
        
        - 共享集群/分布式集群部署中，每个集群最多创建32个YStream服务，各服务可以运行在集群的任意实例上。

    - 创建YStream服务时会记录日志解析起始点，日志解析起始点后的所有未被解析的归档都不会自动清理。应及时删除不必要的YStream服务，以免妨碍数据库自动清理归档，造成存储空间浪费。

    - 若目标YStream服务的start_scn过旧（SCN对应的UNDO日志已被清理），启动时会提示“snapshot too old”。如遇此类情况，建议先删除目标YStream服务再重新创建同名YStream服务并指定新的start_scn。

- YStream服务仅解析创建时所指定的start_scn之后的日志。请勿将start_scn设置在某个DDL事务中间，否则YStream无法通过不完整的DDL日志（start_scn后的部分）构建元数据，会导致该DDL相关的后续日志无法解析并被直接忽略。创建并启动YStream服务后，可执行DBMS_YSTREAM_ADM.TEST校验start_scn是否合理；若出现上述异常场景，校验过程将报错，此时需重新创建YStream服务并指定合适的start_scn。

- YStream服务的默认解析范围是数据库中的所有表，如需自定义配置解析范围则需在创建后执行DBMS_YSTREAM_ADM.ADD_TABLES进行指定：

    - 如需将存量表添加到某个Ystream服务的解析范围中，不建议在服务处于STARTED或RUNNING状态时操作。否则，必须确保执行DBMS_YSTREAM_ADM.ADD_TABLES前目标表未开启[表级附加日志](../SQL参考手册/SQL语句/ALTER TABLE.md#addsupplementalloggingclause)并且在执行DBMS_YSTREAM_ADM.ADD_TABLES后再立即手动开启该表的表级附加日志，开启后Ystream服务才会开始对该表进行解析。

    - 若某个YStream服务的当前解析范围为所有表，在其STARTED或RUNNING状态无法通过DBMS_YSTREAM_ADM.ADD_TABLES指定具体的解析对象（即缩小解析范围），需先停止服务再执行DBMS_YSTREAM_ADM.ADD_TABLES。

- 若YStream服务启动于备库/备集群上（即在备库/备集群上执行的DBMS_YSTREAM_ADM.START），应等待备库回放Ystream系统表日志直至在备库/备集群上查询到YStream服务已被成功启动（[V$YSTREAM_SERVER](../../参考手册/系统视图/动态视图/V$YSTREAM_SERVER.md)视图的STATUS字段为STARTED），再用YStream API客户端开始解析，否则可能报YStream服务状态错误。

- YStream API客户端连接YStream服务必须一对一，并且客户端只能连接启动对应YStream服务的数据库节点，同一个YStream服务不能同时连接多个YStream API客户端。

## 支持同步的数据

### HEAP表和TAC表

YStream支持解析HEAP表和TAC表的DDL、DML及主键约束，支持的DDL范围如下表所示。

|  DDL类型| 同步范围| 示例|
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE TABLE                                       | 列名、列类型、默认值、主键 | CREATE TABLE YDS.TEST(ID INT DEFAULT 1 PRIMARY KEY)          |
| ALTER TABLE RENAME xxx TO                          | -                          | ALTER TABLE RENAME YDS.TEST TO TEST1                         |
| DROP TABLE                                         | -                          | DROP TABLE YDS.TEST                                          |
| TRUNCATE TABLE                                     | -                          | TRUNCATE TABLE YDS.TEST                                      |
| ALTER TABLE ADD COLUMN                             | 列名、列类型、默认值、主键 | ALTER TABLE YDS.TEST ADD COLUMN COL0 INT DEFAULT 1 PRIMARY KEY |
| ALTER TABLE DROP COLUMN                            | -                          | ALTER TABLE YDS.TEST DROP COLUMN ID                          |
| ALTER TABLE RENAME COLUMN                          | -                          | ALTER TABLE YDS.TEST RENAME COLUMN ID TO ID1                 |
| ALTER TABLE MODIFY                                 | 列类型、默认值、主键       | ALTER TABLE YDS.TEST MODIFY (ID INT DEFAULT 1 PRIMARY KEY)   |
| ALTER TABLE ADD PRIMARY KEY                        | -                          | ALTER TABLE YDS.TEST ADD PRIMARY KEY (ID)                    |
| ALTER TABLE ADD CONSTRAINT xxx PRIMARY KEY         | -                          | ALTER TABLE YDS.TEST ADD CONSTRAINT PK PRIMARY KEY (ID)      |
| ALTER TABLE DROP PRIMARY KEY                       | -                          | ALTER TABLE YDS.TEST DROP PRIMARY KEY                        |
| ALTER TABLE DROP CONSTRAINT primary_key_constraint | -                          | ALTER TABLE YDS.TEST DROP CONSTRAINT PK（约束名为`PK`的约束为主键约束） |

此外，对于使用了部分特殊功能的表，Ystream解析还存在以下约束：

- 对于包含自增列（mysql模式独有）的HEAP表，仅在对端是YashanDB（mysql模式）或MySQL数据库时才能同步，否则可能同步失败。

- 对于包含身份列的HEAP表，YStream无法解析该表数据。

- 对于包含虚拟列的HEAP表，YStream仅支持解析DDL，无法解析DML。

- 包含加密列的HEAP表、加密HEAP表均不会记录附加日志，因此YStream无法解析加密表。

- 包含字典编码列的TAC表不会记录附加日志，因此YStream无法解析TAC表。


### Sequence

YStream仅支持同步YashanDB（yashan模式）中的Sequence，支持的DDL范围如下：

|  DDL类型| 同步范围| 示例|
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE|Sequence的所有属性：hwm、max、min、cycle、order、cache、increment| CREATE SEQUENCE YDS.TEST_SEQ |
| ALTER SEQUENCE|修改的Sequence属性| ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2|
| DROP SEQUENCE|Sequence的所有属性：hwm、max、min、cycle、order、cache、increment| DROP SEQUENCE YDS.TEST_SEQ     |

如需同步Sequence的HWM（High Water Mark，高水位），请在YStream API中启用enableSequenceNextVal，即可通过API获取Sequence的HWM更新逻辑日志，同步范围包含Sequence的模式、名称及当前HWM。

共享集群/分布式集群中，如果Sequence设置为NOORDER，YashanDB仅保证各实例内Sequence有序，同步时YStream会同时解析所有实例的日志，解析所得序列可能出现乱序、重复。如需保证共享集群中的Sequence顺序，请设置序列的[ORDER](../SQL参考手册/SQL语句/CREATE SEQUENCE.md#sequence_order)属性。

### 其他DDL

YStream支持同步其他DDL的SQL语句字符串。 
