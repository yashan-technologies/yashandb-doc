## 功能简介

描述结果集的列，返回列名、类型、列大小、小数位数以及是否可为null。

## 函数声明

```c
SQLRETURN SQLDescribeCol(  
      SQLHSTMT       StatementHandle,  
      SQLUSMALLINT   ColumnNumber,  
      SQLCHAR *      ColumnName,  
      SQLSMALLINT    BufferLength,  
      SQLSMALLINT *  NameLengthPtr,  
      SQLSMALLINT *  DataTypePtr,  
      SQLULEN *      ColumnSizePtr,  
      SQLSMALLINT *  DecimalDigitsPtr,  
      SQLSMALLINT *  NullablePtr);
```

## 参数说明

|  参数名| 说明|
| ---------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)   | 语句句柄。                                                   |
| ColumnNumber (IN)      | 结果数据的列数，按顺序递增列顺序排序。                       |
| ColumnName (OUT)       | 指向以null结尾的缓冲区的指针，该缓冲区将返回列名。           |
| BufferLength (IN)      | *ColumnName缓冲区的长度（以字符为单位）。                    |
| NameLengthPtr (OUT)    | 指向缓冲区的指针，该缓冲区将返回可在*ColumnName中返回的字符总数。 |
| DataTypePtr (OUT)      | 指向要在其中返回列的 SQL 数据类型的缓冲区的指针。            |
| ColumnSizePtr (OUT)    | 指向缓冲区的指针，返回数据源上列大小（以字符为单位）。       |
| DecimalDigitsPtr (OUT) | 指向缓冲区的指针，该缓冲区将返回数据源上列的十进制位数。     |
| NullablePtr (OUT)      | 指向缓冲区的指针，该缓冲区将返回一个值，该值指示列是否允许null值。 |
