YashanDB v23.4单机形态增加了MySQL5.7的语法兼容，支持业务系统从MySQL5.7迁移到YashanDB。

## 产品架构

mysql模式产品架构如下图。

![](./image/mysql_arch.png)

YashanDB在原有产品架构中增加了MySQL生态接口、MySQL协议监听、MySQL SQL语法解析器、MySQL SQL执行器能力，可实现与MySQL生态的无缝对接，保留MySQL协议和语法兼容性；同时在兼容性基础上，不损失YashanDB企业级的高可用、安全性、可靠性、灵活扩展性，为企业应用开发提供了更多可能性。

当YashanDB部署为mysql模式时，请遵循如下要求操作数据库：

1. 无论以YashanDB原厂配套的工具驱动（C/JDBC驱动或yasql工具），还是以完成适配的第三方工具驱动（MySQL JDBC驱动、mysql客户端或者Navicat工具等）连接到YashanDB，均需遵循MySQL语法执行业务SQL，包含DDL/DML/DQL语句；

2. 在数据库运维管理场景，需通过YashanDB原厂配套的工具驱动进行操作，且进行数据库管理操作前需将会话参数`COMPAT_VECTOR`切换为yashan模式，如操作命令简单则可以通过`yasql -compat yashan`关键字通过yasql工具直接执行。必须在yashan模式执行的运维操作包含：



    -  备份恢复
    -  闪回
    -  实例启停
    -  主备切换
    -  数据同步
    -  日志管理
    -  文件管理
    -  表空间及表空间集管理
    -  表管理
    -  资源管理
    -  故障诊断
    -  会话和计划管理



3. 如果以YashanDB原厂配套的工具驱动连接数据库，修改会话参数`COMPAT_VECTOR`为yashan模式后，也可以按照原生yashan模式SQL语法进行DDL/DML/DQL操作，但考虑到该场景SQL语义跟mysql和yashan均有差别，需要详细了解一下yashan和mysql的SQL语法差异避免对SQL执行结果出现误解，同时我方也建议避免模式混用。

4. 如需使用mysql模式下的YashanDB PL存储过程能力，需遵循YashanDB的PL语法，但存储过程内的静态和动态SQL语句会按照会话参数`COMPAT_VECTOR`配置的兼容模式进行解析，如没有调整会话参数`COMPAT_VECTOR`，则默认为mysql模式。YashanDB PL存储过程能力请参考[PL参考手册](../开发手册/PL参考手册/00PL参考手册.md)。

5. 如果以第三方工具驱动连接到YashanDB mysql模式，考虑到第三方工具驱动兼容性问题，不建议修改会话进程参数`COMPAT_VECTOR`为yashan模式进行数据库操作。

## 概念说明

YashanDB原生yashan模式与mysql模式在产品概念体系上存在区别，在使用过程中，建议您使用同一套概念体系进行数据库操作。

|  名词| yashan模式（同Oracle）| mysql模式|
|--------------------|-----------------------|---------------|
| 数据库（Database） | 由表空间、数据文件等组成的完整实例<br/>一套YashanDB环境就是1个Database | 数据库对象（表、视图等）的逻辑容器 |
| 模式（Schema） | 与用户一一对应，是用户的数据库对象（表、视图等）的逻辑容器<br/>创建用户时，自动创建1个同名schema | 数据库的别名  |
| 用户（User） | 登录账号 + 同名模式拥有者（天然具备该模式下对象的全部权限）<br/>可被授予其他模式对象的权限 | 仅为登录账号<br/>可被授予任意数据库的权限 |
| 角色（Role） | 权限的集合 | 无<br/>可以使用YashanDB内置的角色（使用时基于yashan语法解析相关SQL语句） |

## 兼容性说明

YashanDB 23.4版本主要完成的MySQL兼容项主要包含如下方向：

- 以mysql模式安装YashanDB数据库管理系统，会默认开启单独的监听端口，处理通过MySQL协议向YashanDB服务端发起的连接、执行等协议命令。

- 支持MySQL特有数据类型兼容，字符集和字符排序规则兼容，语法解析、语句执行将按照MySQL 5.7的模式进行执行；

- 支持MySQL 80多个特有函数，涵盖时间日期、字符处理、数学运算、JSON、流程控制、系统信息、密码校验等多个领域；

- 表达式支持ANSI_QUOTES、NO_BACKSLASH_ESCAPES、PIPES_AS_CONCAT、REAL_AS_FLOAT、PAD_CHAR_TO_FULL_LENGTH等SQL_MODE配置，校验数据合法性，强制规范SQL语法；兼容MySQL变量类型，涵盖用户变量、会话级变量和系统级变量，支持完整的变量管理、查询方式；

- 运维与监控兼容，支持INFORMATION_SCHEMA、PERFORMANCE_SCHEMA和MySQL视图全面兼容，支持SHOW STATUS/SHOW VARIABLES等运维命令；

- 支持可插拔服务插件，在插件模型基础上，允许开发更多的兼容模式；

- 支持部分版本的MySQL Client、mysqldump、mydumper及MySQL C/Java驱动连接；

## 使用约束



使用mysql模式时，存在如下约束：

|  约束项| 约束行为|
| ----------------------- |-------------------------------------------|
| 部署形态       |仅单机或单机主备部署时可选为mysql模式。                                       |
| 表类型                  | mysql模式下仅支持创建行存表。                           |
| 用户登录                | * 如需使用yashan模式下创建的用户登录YashanDB（mysql模式），则要求客户端支持SHA256插件。<br/>* 如需使用yasql以mysql模式下创建的用户登录YashanDB（mysql模式）需要使用双引号将用户名包围。                                   |
| 用户删除和修改          | 不允许在mysql模式下删除和修改yashan模式下创建的用户。         |
| 对象名称                | lower_case_table_names参数仅支持设置为1。<br/>schema、表、视图、表别名的名称按照小写展示。<br/>对象名称匹配按照大小写不敏感的方式匹配。             |
| Binary/Blob类型文本协议 | 当客户端与服务端字符集不一致时，会按服务端字符集转码，可能与客户端预期不一致。      |
| 字符集                  | schema/table/column级的字符集必须与实例级字符集设置一致。 |
| 排序集                  | schema/table/column均可指定字符序且支持实际功能，必须与实例级字符集兼容。 |
| 全局变量                | 全局变量查询和设置仅为语法兼容，除字符集、自动提交外，大部分实际不会生效。     |
| SQL_MODE                | YashanDB mysql模式已实现了部分SQL_MODE语法开关，例如ANSI_QUOTES、NO_AUTO_VALUE_ON_ZERO和NO_BACKSLASH_ESCAPES等，详细兼容项说明请参考[SQL_MODE](SQL参考手册/SQL_MODE)。对于未完成实际能力兼容的开关，无论如何修改参数值，均不会对数据库的行为产生影响，实际执行效果与yashan模式一致。 |
| 可执行注释              | 可执行注释在mysql模式下会被视为注释，对语句执行不产生影响。      |

