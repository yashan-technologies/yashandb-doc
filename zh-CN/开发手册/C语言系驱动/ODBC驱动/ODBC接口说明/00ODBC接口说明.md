本节介绍当前版本的YashanDB ODBC驱动。

## 连接数据源

|  接口名称| 接口说明|
| -------- | ---------------- |
| [SQLAllocHandle](连接数据源/SQLAllocHandle) | 获取环境、连接、语句或描述符句柄。                  |
| [SQLConnect](连接数据源/SQLConnect)        | 按数据源名称、用户ID和密码连接到特定驱动程序。             |
| [SQLDriverConnect](连接数据源/SQLDriverConnect) | 通过连接字符串或请求驱动程序管理器和驱动程序显示用户的连接对话框连接到特定驱动程序。 |

## 获取驱动程序和数据源相关信息

|  接口名称| 接口说明|
| ---------------- | ------- |
| [SQLDataSources](获取驱动程序和数据源相关信息/SQLDataSources) | 返回可用数据源的列表。      |
| [SQLGetInfo](获取驱动程序和数据源相关信息/SQLGetInfo) | 返回有关特定驱动程序和数据源的信息。 |
| [SQLGetFunctions](获取驱动程序和数据源相关信息/SQLGetFunctions) | 返回支持的驱动程序函数。             |
| [SQLGetTypeInfo](获取驱动程序和数据源相关信息/SQLGetTypeInfo)   | 返回有关数据源支持的数据类型的信息。    |
|[SQLTables](获取驱动程序和数据源相关信息/SQLTables) | 返回存储在特定数据源中的表名和列表。               |
|[SQLColumns](获取驱动程序和数据源相关信息/SQLColumns) | 返回指定表中的列名的列表。 |

## 设置和检索驱动程序属性

|  接口名称| 接口说明|
| ---------------- | ----------------- |
| [SQLSetConnectAttr](设置和检索驱动程序属性/SQLSetConnectAttr) | 设置连接属性。     |
| [SQLGetConnectAttr](设置和检索驱动程序属性/SQLGetConnectAttr) | 返回连接属性的值。 |
| [SQLSetEnvAttr](设置和检索驱动程序属性/SQLSetEnvAttr) | 设置环境属性。     |
| [SQLGetEnvAttr](设置和检索驱动程序属性/SQLGetEnvAttr) | 返回环境属性的值。 |
| [SQLSetStmtAttr](设置和检索驱动程序属性/SQLSetStmtAttr) | 设置语句属性。     |
| [SQLGetStmtAttr](设置和检索驱动程序属性/SQLGetStmtAttr) | 返回语句属性的值。 |

## 设置和检索描述符字段

|  接口名称| 接口说明|
| ---------------- | ---------------------------- |
| [SQLGetDescField](设置和检索描述符字段/SQLGetDescField) | 返回单个描述符字段的值。       |
| [SQLSetDescField](设置和检索描述符字段/SQLSetDescField) | 设置单个描述符字段。           |

## 准备SQL请求

|  接口名称| 接口说明|
| ---------------- | ----------------------------- |
| [SQLPrepare](准备SQL请求/SQLPrepare)            | 准备一个SQL语句待之后执行。 |
| [SQLBindParameter](准备SQL请求/SQLBindParameter) | 为SQL语句中的参数分配存储。    |

## 提交请求

|  接口名称| 接口说明|
| ---------------- | ----------------------------- |
| [SQLExecute](提交请求/SQLExecute)            | 执行已准备的语句。                  |
| [SQLExecDirect](提交请求/SQLExecDirect)      | 执行语句。             |
| [SQLNumParams](提交请求/SQLNumParams)        | 返回语句中的参数个数。        |
| [SQLParamData](提交请求/SQLParamData) | 与SQLPutData结合使用 ，在执行时提供参数数据。 |
| [SQLPutData](提交请求/SQLPutData) | 发送参数的一部分或全部数据值。 |

## 检索结果及其相关信息

|  接口名称| 接口说明|
| ---------------- | ----------------------------- |
| [SQLRowCount](检索结果及其相关信息/SQLRowCount) | 返回受插入、更新或删除请求影响的行数。              |
| [SQLNumResultCols](检索结果及其相关信息/SQLNumResultCols) | 返回结果集中的列数。          |
| [SQLDescribeCol](检索结果及其相关信息/SQLDescribeCol) | 描述结果集的列。       |
| [SQLColAttribute](检索结果及其相关信息/SQLColAttribute) | 描述结果集内列的属性。        |
| [SQLBindCol](检索结果及其相关信息/SQLBindCol) | 为结果列分配存储并指定数据类型。       |
| [SQLFetch](检索结果及其相关信息/SQLFetch) | 返回多个结果行。       |
| [SQLFetchScroll](检索结果及其相关信息/SQLFetchScroll) | 返回可滚动的结果行。 |
| [SQLGetData](检索结果及其相关信息/SQLGetData)   | 返回结果集的一行的一列的一部分或全部。 |
| [SQLGetDiagField](检索结果及其相关信息/SQLGetDiagField) | 返回诊断数据结构的单个字段的当前值。 |
| [SQLGetDiagRec](检索结果及其相关信息/SQLGetDiagRec) | 返回诊断数据结构的多个字段的当前值。 |

## 终止语句

|  接口名称| 接口说明|
| ---------------- | ----------------------------- |
| [SQLFreeStmt](终止语句/SQLFreeStmt) | 结束语句处理，放弃挂起的结果，并释放与语句句柄关联的所有资源。 |
| [SQLCloseCursor](终止语句/SQLCloseCursor)    | 关闭在语句句柄上打开的游标。         |
| [SQLCancel](终止语句/SQLCancel)              | 取消对语句的处理。             |
| [SQLEndTran](终止语句/SQLEndTran)            | 提交或回滚事务。       |

## 终止连接

|  接口名称| 接口说明|
| ---------------- | ----------------------------- |
| [SQLDisconnect](终止连接/SQLDisconnect)     | 关闭连接。             |
| [SQLFreeHandle](终止连接/SQLFreeHandle) | 释放环境、连接、语句或描述符句柄。                  |
