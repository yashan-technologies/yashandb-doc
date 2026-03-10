ODBC（Open Database Connectivity，开放数据库互连）提供了一种标准的API（应用程序编程接口）方法来访问数据库，这些API利用SQL来完成其大部分任务。标准的ODBC接口定义了如下内容：

- ODBC函数调用库，应用程序通过它可以连接到数据源，执行SQL命令并检索结果
- 基于SQL-99规范的SQL语法
- 一套标准的错误码
- 一套连接并登录到数据源的标准方式
- 对数据类型的标准描述

仅当YashanDB部署为yashan模式时支持使用该驱动进行连接。

YashanDB ODBC驱动是YashanDB对ODBC API的实现。ODBC API官方文档可参考[ODBC API 参考 - ODBC API Reference | Microsoft Learn](https://learn.microsoft.com/zh-cn/sql/odbc/reference/syntax/odbc-api-reference?view=sql-server-ver16)。
