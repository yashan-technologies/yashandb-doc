## 功能简介

设置环境属性。

## 函数声明

```c
SQLRETURN SQLSetEnvAttr(  
     SQLHENV        EnvironmentHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER *   StringLength);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| EnvironmentHandle (IN) | 环境句柄。                                              |
| Attribute (IN)         | 要设置的属性。                                              |
| ValuePtr (IN) | 指向要与 Attribute 关联的值的指针。 |
| StringLength (IN) | 如果 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |

Attribute参数的支持情况：

|  Attribute| 说明| 支持情况|
| -------------------------------------- | ------------------------------------------------------------ | -------- |
| SQL_ATTR_ODBC_VERSION (ODBC 3.0)       | 该值确定ODBC版本支持<br />SQL_OV_ODBC3_80 = 驱动程序管理器和驱动程序表现出 ODBC 3.8 行为。<br />SQL_OV_ODBC3 = 驱动程序管理器和驱动程序表现出 ODBC *3.x* 行为。<br />SQL_OV_ODBC2 = 驱动程序管理器和驱动程序表现出以下 ODBC *2.x* 行为。<br />该函数无默认值，必须在SQLHENV 参数的任何函数之前设置此环境属性。<br />当前仅支持SQL_OV_ODBC3 | 支持     |
| SQL_ATTR_OUTPUT_NTS (ODBC 3.0)         | 该值确定字符串返回标准<br />SQL_TRUE = 驱动程序将返回字符串数据 null 终止。（默认）<br />SQL_FALSE = 驱动程序不会返回字符串数据 null 终止。<br />当前仅支持SQL_TRUE | 支持     |
| SQL_ATTR_CONNECTION_POOLING (ODBC 3.8) | 该值确定如何在环境级别启用或禁用连接池<br />SQL_CP_OFF  = 连接池已关闭。（默认）<br />SQL_CP_ONE_PER_DRIVER = 每个驱动程序都支持单个连接池。 池中的每个连接都与一个驱动程序相关联。<br />SQL_CP_ONE_PER_HENV = 每个环境都支持单个连接池。 池中的每个连接都与一个环境相关联。<br />SQL_CP_DRIVER_AWARE = 使用驱动程序的连接池感知功能（如果可用）。 | 不支持   |
| SQL_ATTR_CP_MATCH (ODBC 3.0)           | 该值确定如何从连接池中选择连接<br />SQL_CP_STRICT_MATCH = 仅重复使用与调用中的连接选项完全匹配的连接以及应用程序设置的连接属性。（默认）<br />SQL_CP_RELAXED_MATCH = 可以使用具有匹配连接字符串关键字的连接。 <br />关键字必须匹配，但并非所有连接属性都必须匹配。 | 不支持   |
