## 功能简介

释放环境、连接、语句或描述符句柄。

## 函数声明

```c
SQLRETURN SQLFreeHandle(  
     SQLSMALLINT   HandleType,  
     SQLHANDLE     Handle);
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| HandleType (IN) | 要由SQLFreeHandle释放的句柄类型，详细支持情况请查阅 [SQLAllocHandle](../连接数据源/SQLAllocHandle)。 |
| Handle (IN)     | 要释放的句柄。                                               |
