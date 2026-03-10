## 功能简介

为 SQL 语句中的参数分配存储。

## 函数声明

```c
SQLRETURN SQLBindParameter(  
      SQLHSTMT        StatementHandle,  
      SQLUSMALLINT    ParameterNumber,  
      SQLSMALLINT     InputOutputType,  
      SQLSMALLINT     ValueType,  
      SQLSMALLINT     ParameterType,  
      SQLULEN         ColumnSize,  
      SQLSMALLINT     DecimalDigits,  
      SQLPOINTER      ParameterValuePtr,  
      SQLLEN          BufferLength,  
      SQLLEN *        StrLen_or_IndPtr);
```

## 参数说明

| 参数名                 | 说明                                             |
| ---------------------- | ------------------------------------------------ |
| StatementHandle (IN)   | 语句句柄。                                       |
| ParameterNumber (IN)   | 参数编号，按递增参数顺序排序，从 1 开始。        |
| InputOutputType (IN)   | 参数的类型。                                     |
| ValueType (IN)         | 参数的 C 数据类型。                              |
| ParameterType (IN)     | 参数的 SQL 数据类型。                            |
| ColumnSize (IN)        | 相应参数标记的列或表达式的大小。                 |
| DecimalDigits (IN)     | 相应参数标记的列或表达式的十进制数字。           |
| ParameterValuePtr (IN) | 指向参数数据的缓冲区的指针。                     |
| BufferLength (IN/OUT)  | ParameterValuePtr 缓冲区的长度（以字节为单位）。 |
| StrLen_or_IndPtr (IN)  | 指向参数长度缓冲区的指针。                       |

InputOutputType参数的支持情况：

| InputOutputType               | 说明 | 支持情况 |
| ----------------------------- | ---- | -------- |
| SQL_PARAM_INPUT               |      | 支持     |
| SQL_PARAM_OUTPUT              |      | 支持     |
| SQL_PARAM_INPUT_OUTPUT        |      | 不支持   |
| SQL_PARAM_INPUT_OUTPUT_STREAM |      | 不支持   |
| SQL_PARAM_OUTPUT_STREAM       |      | 不支持   |

StrLen_or_IndPtr参数的支持情况：

| StrLen_or_IndPtr                              | 说明                                  | 支持情况 |
| --------------------------------------------- | ------------------------------------- | -------- |
| 存储在 \*ParameterValuePtr 中的参数值的长度。 | 除字符或二进制 C 数据外，将忽略此项。 | 支持     |
| SQL_NTS                                       | 参数值是 null 终止的字符串。          | 支持     |
| SQL_NULL_DATA                                 | 参数值为 NULL。                       | 支持     |
| SQL_DEFAULT_PARAM                             |                                       | 不支持   |
| SQL_LEN_DATA_AT_EXEC                          | 参数的数据将使用 SQLPutData 发送。    | 支持     |
| SQL_DATA_AT_EXEC                              | 参数的数据将使用 SQLPutData 发送。    | 支持     |

> **Note**：
> 预编译SQL中如果使用`:name`形式占位符且多处同名，按位置绑定值时需要绑定多次。通常同名占位符位置绑定的是相同值。
