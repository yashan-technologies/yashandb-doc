## 功能简介

为结果列分配存储并指定数据类型。必须按列绑定。

目前输出数据缓冲区取数据不支持变长类型截断，取结果集数据时，所有变长类型遇到大小不足的输出数据缓冲区会报错。

## 函数声明

```c
SQLRETURN SQLBindCol(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   ColumnNumber,  
      SQLSMALLINT    TargetType,  
      SQLPOINTER     TargetValuePtr,  
      SQLLEN         BufferLength,  
      SQLLEN *       StrLen_or_IndPtr);
```

## 参数说明

|  参数名| 说明|
| ------------------------- | --------------------------------------------- |
| StatementHandle (IN)      | 语句句柄。                                    |
| ColumnNumber (IN)         | 要绑定的结果集列的数目。                      |
| TargetType (IN)           | *TargetValuePtr缓冲区的C数据类型的标识符。    |
| TargetValuePtr (IN/OUT)   | 指向要绑定到列的数据缓冲区的指针。            |
| BufferLength (IN)         | *TargetValuePtr缓冲区的长度（以字节为单位）。 |
| StrLen_or_IndPtr (IN/OUT) | 指向要绑定到列的长度/指示器缓冲区的指针。     |

StrLen_or_IndPtr参数的支持情况：

以下为作为可返回的结果：

|  StrLen_or_IndPtr| 说明| 支持情况|
| -------------------------------------------- | ---------------------- | -------- |
| 存储在 **ParameterValuePtr** 中的结果的长度。 | 可用于返回的数据的长度 | 支持     |
| SQL_NULL_DATA                                | 结果为空               | 支持     |
