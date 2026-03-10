## 功能简介

返回环境属性的值。

## 函数声明

```c
SQLRETURN SQLGetEnvAttr(  
     SQLHENV        EnvironmentHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| EnvironmentHandle (IN) | 环境句柄。                                              |
| Attribute (IN)         | 要检索的属性。                                              |
| ValuePtr (OUT)    | 指向缓冲区的指针，在该缓冲区中返回由 Attribute 指定的特性的当前值。 |
| BufferLength (IN)    | 如果 ValuePtr 指向字符串，则此参数应为 *ValuePtr 的长度。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区要返回的总字节数。 |

Attribute参数的支持情况：

|  Attribute| 说明| 支持情况|
| --------------------------- | -------------------------------------------- | -------- |
| SQL_ATTR_CONNECTION_POOLING | 返回SQL_CP_OFF                               | 支持     |
| SQL_ATTR_ODBC_VERSION       | 默认是SQL_OV_ODBC3，当前也只支持SQL_OV_ODBC3 | 支持     |
| SQL_ATTR_OUTPUT_NTS         | 返回SQL_TRUE                                 | 支持     |
