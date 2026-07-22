## 前言

在Java开发领域，数据库操作是绝大多数企业级应用、后端服务的核心需求——从用户信息存储、交易记录留存，到海量数据统计分析，都离不开Java程序与数据库的高效交互。而JDBC，作为Java语言访问数据库的官方标准，正是打通Java程序与各类数据库之间壁垒的关键桥梁，支撑着无数Java应用的稳定运行。

## JDBC的定义

JDBC的全称是Java DataBase Connectivity（Java数据库连接），它并非一款具体的数据库产品，也不是一个可直接运行的工具，而是由Oracle公司（原Sun公司）制定的一套标准应用程序接口（Java API）规范。

其核心本质是：为Java程序员提供统一的编程接口，屏蔽不同数据库（如Oracle、MySQL、YashanDB、PostgreSQL等）的底层实现差异，让开发者无需针对不同数据库编写不同的访问代码，只需遵循JDBC规范，就能通过统一的代码操作各类关系型数据库。

## JDBC的作用

JDBC规范的核心目标是"统一接口、屏蔽差异、简化开发"，具体而言，它主要实现以下4个核心功能，覆盖数据库操作的全流程：

1. 建立Java程序与数据库的连接：通过JDBC提供的接口，开发者可以指定数据库的地址、用户名、密码等信息，建立程序与数据库之间的稳定通信链路，这是所有数据库操作的前提。

2. 向数据库发送SQL语句：连接建立后，Java程序可通过JDBC接口，向数据库发送查询（SELECT）、插入（INSERT）、更新（UPDATE）、删除（DELETE）等各类SQL语句，实现对数据的操作。

3. 处理数据库返回的结果：数据库执行SQL语句后，会返回相应的结果（如查询到的数据、操作影响的行数），JDBC会将这些结果封装成Java程序可识别的对象，供开发者获取和处理。

4. 管理数据库连接与异常：JDBC提供了连接管理、事务控制（如提交、回滚）的接口，同时定义了统一的异常处理机制，帮助开发者规避连接泄露、操作失败等问题，保障程序的稳定性。

对于Java开发者而言，掌握JDBC是基础也是核心——无论是直接使用JDBC编写数据库操作代码，还是使用MyBatis等框架，理解JDBC的底层原理和工作机制，都能帮助我们更好地应对数据库开发中的各类问题，写出更高效、更稳定的代码。

## YashanDB JDBC简介

YashanDB JDBC驱动是YashanDB对Java API的实现，包括SQL包里的如下接口：

- Connection：JDBC客户端到数据库的连接。
- DatabaseMetaData：获取数据库的综合信息。
- Statement：向数据库发送SQL命令，及从数据库获取结果集 。
- PreparedStatement：预编译SQL语句。
- ParameterMetaData：获取PreparedStatement中绑定参数的类型与属性信息。
- CallableStatement：调用PL对象。
- ResultSet：SQL查询语句执行完成后的结果集。
- ResultSetMetadata：获取结果集中列的类型和属性信息。
- Driver：JDBC驱动接口。
- DataSource/PooledConnection/ConnectionPoolDataSource：数据源/池化的连接/连接池数据源。
- Blob/Clob：操作大对象数据。

除了以上接口，YashanDB JDBC驱动提供了一个自有的用于调试存储过程的调试器接口。对以上接口的具体实现见[JDBC接口支持说明](JDBC参考信息/JDBC接口支持说明/00JDBC接口支持说明)章节描述。

在YashanDB的每个版本的产品安装包中，均会配套提供JDBC驱动jar包，开发人员安装后即可使用标准的JDBC和SQL语言连接和操作YashanDB。具体安装步骤见[安装YashanDB JDBC驱动](安装JDBC驱动)。

查看[JDBC快速入门](JDBC快速入门/00JDBC快速入门)可以了解在Java应用程序里连接并操作YashanDB的简单的过程。

YashanDB JDBC驱动还包括如下主要功能：

- Java与YashanDB之间的数据类型转换，具体见[JDBC数据类型映射关系](JDBC参考信息/JDBC数据类型映射关系/00JDBC数据类型映射关系)章节描述。
- 在高可用的主备部署中，自动识别并连接主库。
- 在负载均衡的共享集群/分布式集群和存算一体分布式集群部署中，自动识别并连接负载最小的节点。
- 超时机制，包括连接超时、socket超时、SQL执行超时等。
- 透明应用故障转移功能，在当前连接节点发生故障后，客户端能够自动重新连接到已配置的其他节点，具体见[JDBC透明应用故障转移](JDBC使用指南/透明应用故障转移)章节描述。

> **Note**:
>
> 若存在JDBC相关适配问题，请联系我们的技术支持解决。
