## 功能简介

返回可用数据源的列表。

当前函数为驱动程序管理器实现，对Windows自带的驱动程序管理器和linux下的Unix ODBC都已支持。

## 函数声明

```c
SQLRETURN SQLDataSources(  
     SQLHENV          EnvironmentHandle,  
     SQLUSMALLINT     Direction,  
     SQLCHAR *        ServerName,  
     SQLSMALLINT      BufferLength1,  
     SQLSMALLINT *    NameLength1Ptr,  
     SQLCHAR *        Description,  
     SQLSMALLINT      BufferLength2,  
     SQLSMALLINT *    NameLength2Ptr);
```

## 参数说明

|  参数名| 说明|
| ------------ |---------------------------------------------------------------------------------------------------------------------------------------------------|
| EnvironmentHandle (IN) | 环境句柄。                                                                                                                                           |
| Direction (IN) | 确定驱动程序管理器返回有关哪个数据源的信息。 |
| ServerName (OUT) | 指向要在其中返回数据源名称的缓冲区的指针。 |
| BufferLength1 (IN) | *ServerName 缓冲区的长度。 |
| NameLength1Ptr (OUT) | 指向缓冲区的指针，该缓冲区将返回总字符数。 |
| Description (OUT) | 指向缓冲区的指针，该缓冲区将返回与数据源关联的驱动程序的说明。 |
| BufferLength2 (IN) | *Description缓冲区的长度（以字符为单位）。 |
| NameLength2Ptr (OUT) | 指向缓冲区的指针，该缓冲区要返回总字符数。 |
