## 功能简介

获取环境、连接、语句或描述符句柄。

## 函数声明

```c
SQLRETURN SQLAllocHandle(  
      SQLSMALLINT   HandleType,  
      SQLHANDLE     InputHandle,  
      SQLHANDLE *   OutputHandlePtr);
```

## 参数说明

| 参数名                | 说明                                                     |
| --------------------- | -------------------------------------------------------- |
| HandleType (IN)       | 要由 SQLAllocHandle 分配的句柄的类型。                   |
| InputHandle (IN)      | 要为其上下文分配新句柄的输入句柄。                       |
| OutputHandlePtr (OUT) | 指向缓冲区的指针，该缓冲区将句柄返回到新分配的数据结构。 |

HandleType参数的支持情况：

| HandleType                | 说明                             | 支持情况 |
| ------------------------- | -------------------------------- | -------- |
| SQL_HANDLE_DBC            |                                  | 支持     |
| SQL_HANDLE_DBC_INFO_TOKEN | 仅由驱动程序调用，ODBC应用不感知 | 不支持   |
| SQL_HANDLE_DESC           | 目前不支持显式创建描述符         | 不支持   |
| SQL_HANDLE_ENV            |                                  | 支持     |
| SQL_HANDLE_STMT           |                                  | 支持     |
