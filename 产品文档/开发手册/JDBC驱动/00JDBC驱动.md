JDBC（Java DataBase Connectivity）是一套标准的用于在Java语言中访问数据库的应用程序接口（Java API)，Java程序员通过SQL包中定义的一系列抽象类对数据库进行操作。

YashanDB JDBC驱动是YashanDB对Java API的实现，包括SQL包里的如下接口：

- Connection：JDBC客户端到数据库的连接。
- DatabaseMetaData：获取数据库的综合信息。
- Statement：向数据库发送SQL命令，及从数据库获取结果集 。
- PreparedStatement：预编译SQL语句。
- ParameterMetaData：获取PreparedStatement中绑定参数的类型与属性信息。
- CallableStatement：PL对象调用。
- ResultSet：SQL查询语句执行完成后的结果集 。
- ResultSetMetadata：获取结果集中列的类型和属性信息 。
- Driver：JDBC驱动接口。
- DataSource/PooledConnection/ConnectionPoolDataSource：数据源/到数据源的连接/连接池。
- Blob/Clob：对大对象数据的操作接口。

除了以上接口，YashanDB JDBC驱动提供了一个自有的用于调试存储过程的调试器接口。对以上接口的具体实现见[JDBC接口支持说明](JDBC接口支持说明/00JDBC接口支持说明)章节描述。

在YashanDB的每个版本的产品安装包中，均会配套提供JDBC驱动jar包，开发人员安装后即可使用标准的JDBC和SQL语言连接和操作YashanDB。具体安装步骤见[安装YashanDB JDBC驱动](安装YashanDB JDBC驱动)。

查看[YashanDB JDBC驱动使用介绍](YashanDB JDBC驱动使用介绍)可以了解在Java应用程序里连接并操作YashanDB的简单的过程。

YashanDB JDBC驱动还包括如下主要功能：

- Java与YashanDB之间的数据类型转换，具体见[JDBC数据类型映射关系](JDBC数据类型映射关系/00JDBC数据类型映射关系)章节描述。
- 在高可用的主备部署中，自动识别并连接主库。
- 在负载均衡的共享集群和分布式部署中，自动识别并连接负载最小的节点。
- 超时机制，包括连接超时、socket超时、SQL执行超时等。
- 透明应用程序故障转移功能，在当前连接节点发生故障后，客户端能够自动重新连接到已配置的其他节点，具体见[JDBC透明应用程序故障转移](JDBC高级功能说明/透明应用程序故障转移)章节描述。

> **Note**:
> 
> 若存在JDBC相关适配问题，请联系我们的技术支持解决。