## 功能简介

返回有关特定驱动程序和数据源的信息。

该函数中返回的数据库支持情况可能并非最新版本，如需获取最新版本信息，请联系我们的技术支持。

## 函数声明

```c
SQLRETURN SQLGetInfo(  
     SQLHDBC         ConnectionHandle,  
     SQLUSMALLINT    InfoType,  
     SQLPOINTER      InfoValuePtr,  
     SQLSMALLINT     BufferLength,  
     SQLSMALLINT *   StringLengthPtr);
```

## 参数说明

|  参数名| 说明|
| --------------------- | ------------------------------------------------------------ |
| ConnectionHandle (IN) | 连接句柄。                                                   |
| InfoType (IN)         | 信息类型                                                     |
| InfoValuePtr (OUT)    | 指向要在其中返回信息的缓冲区的指针。                         |
| BufferLength (OUT)    | \*InfoValuePtr缓冲区的长度。 如果\*InfoValuePtr 中的值不是字符串，或者 如果\* InfoValuePtr 是空指针，则忽略 BufferLength 参数。 |
| StringLengthPtr (OUT) | 指向缓冲区的指针，该缓冲区要返回的总字节数（不包括字符数据的 null 终止字符数），可在 \*InfoValuePtr 中返回。 |
