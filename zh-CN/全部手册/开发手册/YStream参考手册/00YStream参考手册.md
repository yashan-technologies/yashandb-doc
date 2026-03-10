YStream是YashanDB提供的逻辑日志解析接口，客户端API采用Java代码实现。

客户端软件可以集成YStream API的jar包，在已开启附加日志（[库级](../SQL参考手册/SQL语句（yashan模式）/ALTER DATABASE.html#supplementallogclauses)或[表级](../SQL参考手册/SQL语句（yashan模式）/ALTER DATABASE.html#addsupplementalloggingclause)）的YashanDB数据库环境中，调用API接口获取YashanDB逻辑日志，包括DML和DDL。

YStream不适用于存算一体分布式集群部署。

## 准备工作

### 主备模式环境要求

在备库上使用YStream时，应确保主备实例或主备集群间网络连接快速、稳定，否则可能出现网络连接断开异常；备实例的计算、存储性能不低于主实例，否则主备实例日志差距过大可能引起超时异常；Switchover备实例的YStream服务会与主实例断开连接后重连，备实例YStream可能出现网络连接断开异常，建议在YStream服务停止后再执行Switchover操作；主实例shutdown或其他原因异常退出，备实例YStream也可能出现网络连接断开异常，建议在YStream服务停止后主实例再执行shutdown操作。

### 数据库归档管理

YStream仅支持解析归档模式下生成的redo和归档日志，使用YStream时，必须打开数据库[归档](../../数据库管理/基本数据库管理/归档管理)，否则创建YStream服务时报错。

YStream服务创建时，会记录日志解析起始点。由于数据库开启了归档，日志解析起始点后的所有未被解析的归档都不会自动清理。若无需再使用某个服务对应的日志解析，请及时删除不必要的YStream服务，以免妨碍数据库自动清理归档，造成存储空间浪费。

### 配置数据库YStream Server

参考[YStream客户端使用示例](./YStream客户端使用介绍)配置YStream Server，确保YStream服务创建并启动后，通过YStream API开始解析。

## 支持同步的数据

### HEAP表和TAC表

YStream支持HEAP表和TAC表的DDL、DML及主键约束。支持的DDL范围如下：

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

### Sequence

YStream支持同步Sequence，支持的DDL范围如下：

|  DDL类型| 同步范围| 示例|
| -------------------------------------------------- | -------------------------- | ------------------------------------------------------------ |
| CREATE SEQUENCE|Sequence的所有属性：HWM、max、min、cycle、order、cache、increment| CREATE SEQUENCE YDS.TEST_SEQ |
| ALTER SEQUENCE|修改的Sequence属性| ALTER SEQUENCE YDS.TEST_SEQ INCREMENT BY 2|
| DROP SEQUENCE|Sequence的所有属性：HWM、max、min、cycle、order、cache、increment| DROP SEQUENCE YDS.TEST_SEQ     |

如需Sequence同步HWM（High Water Mark，高水位）。请在YStream API中启用enableSequenceNextVal，即可通过API获取Sequence的HWM更新逻辑日志，同步范围包含Sequence的模式、名称及当前HWM。

共享集群中，如果Sequence设置为NOORDER，YashanDB仅保证各实例内Sequence有序，同步时YStream会同时解析所有实例的日志，解析所得序列可能出现乱序、重复。如需保证共享集群中的Sequence顺序，请设置序列的[ORDER](../SQL参考手册/SQL语句（yashan模式）/CREATE SEQUENCE.html#sequence_order)属性。

### 其他DDL
YStream支持同步其他DDL的SQL语句字符串。 
