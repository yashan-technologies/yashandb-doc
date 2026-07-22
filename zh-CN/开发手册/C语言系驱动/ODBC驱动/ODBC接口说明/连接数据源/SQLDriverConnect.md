## 功能简介

通过连接字符串或请求驱动程序管理器和驱动程序显示用户的连接对话框连接到特定驱动程序。

## 函数声明

```c
SQLRETURN SQLDriverConnect(  
     SQLHDBC         ConnectionHandle,  
     SQLHWND         WindowHandle,  
     SQLCHAR *       InConnectionString,  
     SQLSMALLINT     StringLength1,  
     SQLCHAR *       OutConnectionString,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   StringLength2Ptr,  
     SQLUSMALLINT    DriverCompletion);
```

## 参数说明

|  参数名| 说明|
| --------------------- | -------------------------------------- |
| ConnectionHandle (IN) | 连接句柄。                             |
| WindowHandle (IN)       | 窗口句柄。                      |
| InConnectionString (IN) | 完整的连接字符串 、部分连接字符串或空字符串中的语法。 |
| StringLength1 (IN) | *InConnectionString 的长度。  |
| OutConnectionString (OUT) | 指向已完成连接字符串的缓冲区的指针。 |
| BufferLength (IN) | \*OutConnectionString 缓冲区的长度（以字符为单位）。 |
| StringLength2Ptr (OUT) | 指向缓冲区的指针，该缓冲区将返回总字符数。可在 *OutConnectionString 中返回。 |
| DriverCompletion (IN) | 指示驱动程序管理器还是驱动程序必须提示输入更多连接信息的标志。 |

DriverCompletion参数的支持情况：

|  DriverCompletion| 说明| 支持情况|
| ---------------------------- | ------------------------------------------------------------ | -------- |
| SQL_DRIVER_COMPLETE          | 此选项优先尝试使用已有信息登录，登录失败会弹出提示符提示用户输入缺失信息 | 支持     |
| SQL_DRIVER_COMPLETE_REQUIRED | 此选项优先尝试使用已有信息登录，登录失败会弹出提示符提示用户输入缺失信息（同上） | 支持     |
| SQL_DRIVER_NOPROMPT          | 无提示符窗口                                                 | 支持     |
| SQL_DRIVER_PROMPT            | 有提示符窗口                                                 | 支持     |
