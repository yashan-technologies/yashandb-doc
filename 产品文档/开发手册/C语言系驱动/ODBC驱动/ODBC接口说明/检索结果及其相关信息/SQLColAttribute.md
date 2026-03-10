## 功能简介

描述结果集内列的属性。

## 函数声明

```c
SQLRETURN SQLColAttribute (  
      SQLHSTMT        StatementHandle,  
      SQLUSMALLINT    ColumnNumber,  
      SQLUSMALLINT    FieldIdentifier,  
      SQLPOINTER      CharacterAttributePtr,  
      SQLSMALLINT     BufferLength,  
      SQLSMALLINT *   StringLengthPtr,  
      SQLLEN *        NumericAttributePtr);
```

## 参数说明

| 参数名                      | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| StatementHandle (IN)        | 语句句柄。                                                   |
| ColumnNumber (IN)           | 要从中检索字段值的 IRD 中的记录编号。                        |
| FieldIdentifier (IN)        | 描述符句柄。                                                 |
| CharacterAttributePtr (OUT) | 指向缓冲区的指针。                                           |
| BufferLength (IN)           | 如果 FieldIdentifier 是 ODBC 定义的字段，并且 CharacterAttributePtr 指向字符串或二进制缓冲区，则此参数的长度应为 *CharacterAttributePtr。 |
| StringLengthPtr (OUT)       | 指向缓冲区的指针，该缓冲区返回的总字节数（不包括字符数据的 null 终止字节数），可在 *CharacterAttributePtr 中返回。 |
| NumericAttributePtr (OUT)   | 指向整数缓冲区的指针。                                       |

FieldIdentifier参数的支持情况：

| FieldIdentifier            | 说明                                                         | 支持情况 |
| -------------------------- | ------------------------------------------------------------ | -------- |
| SQL_DESC_CONCISE_TYPE      | 简约类型                                                     | 支持     |
| SQL_DESC_DISPLAY_SIZE      | 最大显示宽度，对于变长数据类型（字符串类型和二进制类型），绑定缓冲区大小建议在此基础上+1（用于放置字符串结束符'\0'） | 支持     |
| SQL_DESC_LENGTH            | 等同于SQL_DESC_DISPLAY_SIZE                                  | 支持     |
| SQL_DESC_NAME              | 列名                                                         | 支持     |
| SQL_COLUMN_LABEL           | 等同于SQL_DESC_NAME(ODBC 2.0)                                | 支持     |
| SQL_DESC_NULLABLE          | 可空性                                                       | 支持     |
| SQL_DESC_OCTET_LENGTH      | 等同于SQL_DESC_DISPLAY_SIZE                                  | 支持     |
| SQL_DESC_PRECISION         | 精度                                                         | 支持     |
| SQL_DESC_SCALE             | 刻度                                                         | 支持     |
| SQL_DESC_TYPE              | 返回值根据sqlType不同<br />switch (sqlType) {<br/>    case SQL_TYPE_DATE:<br/>    case SQL_TYPE_TIME:<br/>    case SQL_TYPE_TIMESTAMP:<br/>        *value = SQL_DATETIME;<br/>        break;<br/>    case SQL_INTERVAL_DAY_TO_SECOND:<br/>    case SQL_INTERVAL_YEAR_TO_MONTH:<br/>        *value = SQL_INTERVAL;<br/>        break;<br/>    default:<br/>        *value = sqlType;<br/>        break;<br/>} | 支持     |
| SQL_DESC_UNSIGNED          | 是否为无符号                                                 | 支持     |
| SQL_DESC_AUTO_UNIQUE_VALUE |                                                              | 不支持   |
| SQL_DESC_BASE_COLUMN_NAME  |                                                              | 不支持   |
| SQL_DESC_BASE_TABLE_NAME   |                                                              | 不支持   |
| SQL_DESC_CASE_SENSITIVE    |                                                              | 不支持   |
| SQL_DESC_CATALOG_NAME      |                                                              | 不支持   |
| SQL_DESC_FIXED_PREC_SCALE  |                                                              | 不支持   |
| SQL_DESC_LITERAL_PREFIX    |                                                              | 不支持   |
| SQL_DESC_LITERAL_SUFFIX    |                                                              | 不支持   |
| SQL_DESC_NUM_PREC_RADIX    |                                                              | 不支持   |
| SQL_DESC_SCHEMA_NAME       |                                                              | 不支持   |
| SQL_DESC_SEARCHABLE        |                                                              | 不支持   |
| SQL_DESC_TABLE_NAME        |                                                              | 不支持   |
| SQL_DESC_TYPE_NAME         |                                                              | 不支持   |
| SQL_DESC_UNNAMED           |                                                              | 不支持   |
| SQL_DESC_UPDATABLE         |                                                              | 不支持   |

