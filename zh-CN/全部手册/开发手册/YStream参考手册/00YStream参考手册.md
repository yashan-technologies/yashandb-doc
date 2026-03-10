YStream是YashanDB提供的逻辑日志解析接口，支持Java语言的客户端API。

用户可以在其他软件中集成YStream API的jar包，调用API接口进行数据变更捕获（CDC，Change Data Capture）、获取并解析YashanDB的附加日志，包括DML和DDL。

存算一体分布式集群部署中无YStream功能。

## 准备工作

### 主备模式环境要求

在备库/备集群上运行YStream服务时，需注意：

- 应确保主备库/主备集群间网络连接快速、稳定，否则可能出现网络连接断开异常。

- 应确保备库/备集群的计算、存储性能不低于主库/主集群，否则主备日志差距过大可能引起超时异常。

- 主备发生切换时，备库/备集群上运行的YStream服务也可能出现网络断连异常。如需手动执行计划内切换（Switchover）或故障切换（Failover），建议停止YStream服务后再操作。

- 主库/主集群关闭或因其他原因异常退出时，备库/备集群上运行的YStream服务也可能出现网络断连异常。如需手动关闭主库/主集群，建议停止YStream服务后再操作。

### 数据库附加日志管理

创建YStream服务前，建议先开启附加日志（[库级](../SQL参考手册/SQL语句/ALTER DATABASE.html#supplementallogclauses)或[表级](../SQL参考手册/SQL语句/ALTER DATABASE.html#addsupplementalloggingclause)）。若不开启，则只能解析已有日志中的附加信息，且在已有YStream服务时再开启附加日志会报错。

### 数据库归档管理

YStream仅支持解析归档模式下生成的redo和归档日志，使用YStream时，必须打开数据库[归档](../../数据库管理/实例管理/归档管理)，否则创建YStream服务时报错。

YStream服务创建时，会记录日志解析起始点。由于数据库开启了归档，日志解析起始点后的所有未被解析的归档都不会自动清理。若无需再使用某个YStream服务对应的日志解析，请及时删除不必要的YStream服务，以免妨碍数据库自动清理归档，造成存储空间浪费。

### 配置数据库YStream服务

调用[DBMS_YSTREAM_ADM](../PL参考手册/内置高级包/DBMS_YSTREAM_ADM)高级包创建、配置并启动YStream服务，具体操作可参考[YStream使用示例](./YStream使用示例)。

## 支持同步的数据

### HEAP表和TAC表

YStream支持HEAP表和TAC表的DDL、DML及主键约束，支持的DDL范围如下：

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

> **Note**: 
>
> - 当表包含身份列或自增列（mysql模式）时，YStream无法解析该表数据；
> - 当表包含虚拟列，不会记录DML的附加日志，YStream只能解析DDL，无法解析该表DML。

### Sequence

YStream支持同步Sequence，支持的DDL范围如下：

|  DDL类型| 同步范围| 示例|
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE|Sequence的所有属性：HWM、max、min、cycle、order、cache、increment| CREATE SEQUENCE YDS.TEST_SEQ |
| ALTER SEQUENCE|修改的Sequence属性| ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2|
| DROP SEQUENCE|Sequence的所有属性：HWM、max、min、cycle、order、cache、increment| DROP SEQUENCE YDS.TEST_SEQ     |

如需同步Sequence的HWM（High Water Mark，高水位），请在YStream API中启用enableSequenceNextVal，即可通过API获取Sequence的HWM更新逻辑日志，同步范围包含Sequence的模式、名称及当前HWM。

共享集群/分布式集群中，如果Sequence设置为NOORDER，YashanDB仅保证各实例内Sequence有序，同步时YStream会同时解析所有实例的日志，解析所得序列可能出现乱序、重复。如需保证共享集群中的Sequence顺序，请设置序列的[ORDER](../SQL参考手册/SQL语句/CREATE SEQUENCE.html#sequence_order)属性。

### 其他DDL

YStream支持同步其他DDL的SQL语句字符串。 
