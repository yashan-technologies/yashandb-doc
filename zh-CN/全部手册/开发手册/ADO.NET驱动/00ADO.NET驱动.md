## ADO.NET

ADO.NET是用于.NET语言的核心数据访问技术。 该技术定义了一套对公开数据访问服务的类和接口，面向开发者提供了对关系数据、XML 和应用程序数据的一致访问，因此成为.NET Framework中不可缺少的一部分。

仅当YashanDB部署为yashan模式时支持使用该驱动进行连接。

YashanDB ADO.NET驱动使用Yashandb.Data.YashandbClient命名空间，实现了ADO.NET的大部分类和接口，使得.NET的应用程序可通过ADO.NET的方式访问和操作YashanDB数据库数据。

YashanDB ADO.NET驱动是YashanDB对标准的ADO.NET的实现，包括如下类：

- DbConnection：ADO.NET客户端到数据库的连接。
- DbTransaction：定义数据库事务的核心行为，并为数据库专用事务提供基类。
- DbCommand：向数据库发送SQL命令，及从数据库获取结果集。
- DbParameter：SQL绑定参数方法类。
- DbDataReader：结果集获取方法类。
- DbParameterCollection：与DbCommand相关的参数集合的基类。

对以上内容的具体实现见[ADO.NET接口支持说明](ADO.NET接口支持说明/00ADO.NET接口支持说明)章节描述。

> **Caution**:
>
> YashanDB ADO.NET驱动暂不支持连接以mysql模式安装的YashanDB。
