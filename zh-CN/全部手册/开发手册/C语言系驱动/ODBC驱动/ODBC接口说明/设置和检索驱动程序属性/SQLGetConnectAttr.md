## 功能简介

返回连接属性的值。

## 函数声明

```c
SQLRETURN SQLGetConnectAttr(  
     SQLHDBC        ConnectionHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN) | 连接句柄。                                                   |
| Attribute (IN)         | 要检索的属性。                                              |
| ValuePtr (OUT)    | 指向内存的指针，在该内存中返回由 Attribute 指定的特性的当前值。 |
| BufferLength (IN)    | 如果 Attribute 是 ODBC 定义的属性，并且 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区要返回的总字节数。 |

Attribute参数的支持情况：

|  Attribute| 说明| 支持情况|
| --------------------------- | ---------------------------- | -------- |
| SQL_ATTR_AUTOCOMMIT         | 默认是SQL_TRUE               | 支持     |
| SQL_ATTR_PACKET_SIZE        | 默认是128K                   | 支持     |
| SQL_ATTR_TXN_ISOLATION      | 默认是SQL_TXN_READ_COMMITTED | 支持     |
| SQL_ATTR_ACCESS_MODE        | 返回SQL_MODE_READ_WRITE      | 不支持   |
| SQL_ATTR_AUTO_IPD           | 返回SQL_FALSE                | 不支持   |
| SQL_ATTR_CONNECTION_DEAD    |                              | 不支持   |
| SQL_ATTR_CONNECTION_TIMEOUT |                              | 不支持   |
| SQL_ATTR_CURRENT_CATALOG    | 返回"YashanDB"               | 不支持   |
| SQL_ATTR_ODBC_CURSORS       | 返回SQL_CUR_USE_IF_NEEDED    | 不支持   |
