## 功能简介

返回结果集一行一列的部分或全部数据。 

目前支持以下四种级别的数据获取方式，不支持SQL_GD_OUTPUT_PARAMS：

- **SQL_GD_ANY_COLUMN**：允许对未绑定的列使用SQLGetData，即使列的顺序在已绑定列之后。
- **SQL_GD_ANY_ORDER**：以任意顺序返回未绑定列的数据，无需严格按照列顺序调用SQLGetData。
- **SQL_GD_BOUND**：即使列已绑定，也允许使用SQLGetData获取该列的数据。
- **SQL_GD_BLOCK**：在块行模式下，即使尚未执行取数操作，也可对未绑定列使用SQLGetData。

## 函数声明

```c
SQLRETURN SQLGetData(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   Col_or_Param_Num,  
      SQLSMALLINT    TargetType,  
      SQLPOINTER     TargetValuePtr,  
      SQLLEN         BufferLength,  
      SQLLEN *       StrLen_or_IndPtr);
```

## 参数说明

|  参数名| 说明|
| ---------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)   | 语句句柄。                                                   |
| Col_or_Param_Num (IN) | 对于检索列数据，它是要为其返回数据的列的编号。               |
| TargetType (IN)        | *TargetValuePtr 缓冲区的 C 数据类型的类型标识符，不支持SQL_ARD_TYPE类型。 |
| TargetValuePtr (OUT)   | 指向要在其中返回数据的缓冲区的指针。                         |
| BufferLength (IN)      | *TargetValuePtr 缓冲区的长度（以字节为单位）。               |
| StrLen_or_IndPtr (OUT) | 指向要返回长度或指示器值的缓冲区的指针。                     |
