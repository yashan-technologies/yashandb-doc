## 功能简介

设置单个描述符字段。

调用该函数设置描述符字段时，应用程序的相关设置步骤必须遵循如下顺序：

1. 应用程序必须先设置SQL_DESC_TYPE、SQL_DESC_CONCISE_TYPE或SQL_DESC_DATETIME_INTERVAL_CODE字段。
2. 应用程序设置数据类型的属性，驱动程序会将数据类型属性字段设置为数据类型的相应默认值。如果应用程序显式设置数据类型属性，驱动程序仍会将其重写为默认属性。
   自动默认类型属性字段可确保在应用程序指定数据类型后，描述符始终可供使用。
3. 应用程序设置SQL_DESC_DATA_PTR。此时会提示对描述符字段进行一致性检查，如果应用程序在设置SQL_DESC_DATA_PTR字段后更改数据类型或属性，驱动程序会将SQL_DESC_DATA_PTR设置为null指针，取消绑定记录。

如果调用该函数设置除SQL_DESC_COUNT以外的任何字段或延迟字段SQL_DESC_DATA_PTR、SQL_DESC_OCTET_LENGTH_PTR或SQL_DESC_INDICATOR_PTR，则记录将变为未绑定。设置除SQL_DESC_DATA_PTR、SQL_DESC_INDICATOR_PTR外的记录字段，记录将变为未绑定，SQL_DESC_DATA_PTR将被设为NULL。

## 函数声明

```c
SQLRETURN SQLSetDescField(  
     SQLHDESC      DescriptorHandle,  
     SQLSMALLINT   RecNumber,  
     SQLSMALLINT   FieldIdentifier,  
     SQLPOINTER    ValuePtr,  
     SQLINTEGER    BufferLength);
```

## 参数说明

| 参数名                | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| DescriptorHandle (IN) | 描述符句柄。                                                 |
| RecNumber (IN)        | 指示包含应用程序要设置的字段的描述符记录。                   |
| FieldIdentifier (IN)  | 指示要设置其值的描述符的字段。                               |
| ValuePtr (OUT)        | 指向包含描述符信息或整数值的缓冲区的指针。                   |
| BufferLength (IN)     | 如果 FieldIdentifier 是 ODBC 定义的字段，并且 ValuePtr 指向字符串或二进制缓冲区，则此参数应为 *ValuePtr 的长度。 |

FieldIdentifier参数的支持情况：

不在列表中的参数不支持，同时已做拦截处理。

**ARD**

| 字段属性 | FieldIdentifier                 | 支持情况 | 值说明                                                       |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header   | SQL_DESC_ARRAY_SIZE             | 支持     | 等价于SQLSetStmtAttr的SQL_ATTR_ROW_ARRAY_SIZE                |
| header   | SQL_DESC_BIND_TYPE              | 支持     | 仅支持SQL_BIND_BY_COLUMN                                     |
| record   | SQL_DESC_CONCISE_TYPE           | 支持     |                                                              |
| record   | SQL_DESC_DATA_PTR               | 支持     |                                                              |
| record   | SQL_DESC_DATETIME_INTERVAL_CODE | 支持     | 返回结果根据C Type决定<br />switch (*bindCType) {<br/>    case SQL_DATETIME:<br/>    case SQL_INTERVAL:<br/>    case SQL_C_TYPE_DATE:<br/>    case SQL_C_TYPE_TIME:<br/>    case SQL_C_TYPE_TIMESTAMP:<br/>    case SQL_C_INTERVAL_DAY_TO_SECOND:<br/>    case SQL_C_INTERVAL_YEAR_TO_MONTH:<br/>        switch (value) {<br/>            case SQL_CODE_DATE:<br/>                *bindCType = SQL_C_TYPE_DATE;<br/>            case SQL_CODE_TIME:<br/>                *bindCType = SQL_C_TYPE_TIME;<br/>            case SQL_CODE_TIMESTAMP:<br/>                *bindCType = SQL_C_TYPE_TIMESTAMP;<br/>            case SQL_CODE_DAY_TO_SECOND:<br/>                *bindCType = SQL_C_INTERVAL_DAY_TO_SECOND;<br/>            case SQL_CODE_YEAR_TO_MONTH:<br/>                *bindCType = SQL_C_INTERVAL_YEAR_TO_MONTH;<br/>            default:<br/>                break;<br/>        }<br/>    default:<br/>        break;<br/>} |
| record   | SQL_DESC_INDICATOR_PTR          | 支持     |                                                              |
| record   | SQL_DESC_OCTET_LENGTH           | 支持     |                                                              |
| record   | SQL_DESC_PRECISION              | 支持     |                                                              |
| record   | SQL_DESC_SCALE                  | 支持     |                                                              |
| record   | SQL_DESC_TYPE                   | 支持     | 等价于SQL_DESC_CONCISE_TYPE                                  |

**APD**

| 字段属性 | FieldIdentifier                 | 支持情况 | 值说明                                                       |
| -------- | ------------------------------- | -------- | ------------------------------------------------------------ |
| header   | SQL_DESC_ARRAY_SIZE             | 支持     | 等价于SQLSetStmtAttr的SQL_ATTR_PARAMSET_SIZE                 |
| header   | SQL_DESC_BIND_TYPE              | 支持     | 仅支持SQL_BIND_BY_COLUMN                                     |
| record   | SQL_DESC_CONCISE_TYPE           | 支持     |                                                              |
| record   | SQL_DESC_DATA_PTR               | 支持     |                                                              |
| record   | SQL_DESC_DATETIME_INTERVAL_CODE | 支持     | 返回结果根据C Type决定<br />switch (*bindCType) {<br/>    case SQL_DATETIME:<br/>    case SQL_INTERVAL:<br/>    case SQL_C_TYPE_DATE:<br/>    case SQL_C_TYPE_TIME:<br/>    case SQL_C_TYPE_TIMESTAMP:<br/>    case SQL_C_INTERVAL_DAY_TO_SECOND:<br/>    case SQL_C_INTERVAL_YEAR_TO_MONTH:<br/>        switch (value) {<br/>            case SQL_CODE_DATE:<br/>                *bindCType = SQL_C_TYPE_DATE;<br/>            case SQL_CODE_TIME:<br/>                *bindCType = SQL_C_TYPE_TIME;<br/>            case SQL_CODE_TIMESTAMP:<br/>                *bindCType = SQL_C_TYPE_TIMESTAMP;<br/>            case SQL_CODE_DAY_TO_SECOND:<br/>                *bindCType = SQL_C_INTERVAL_DAY_TO_SECOND;<br/>            case SQL_CODE_YEAR_TO_MONTH:<br/>                *bindCType = SQL_C_INTERVAL_YEAR_TO_MONTH;<br/>            default:<br/>                break;<br/>        }<br/>    default:<br/>        break;<br/>} |
| record   | SQL_DESC_INDICATOR_PTR          | 支持     |                                                              |
| record   | SQL_DESC_OCTET_LENGTH           | 支持     |                                                              |
| record   | SQL_DESC_PRECISION              | 支持     |                                                              |
| record   | SQL_DESC_SCALE                  | 支持     |                                                              |
| record   | SQL_DESC_TYPE                   | 支持     | 等价于SQL_DESC_CONCISE_TYPE                                  |

**IRD**

| 字段属性 | FieldIdentifier             | 支持情况 | 值说明                                          |
| -------- | --------------------------- | -------- | ----------------------------------------------- |
| header   | SQL_DESC_ROWS_PROCESSED_PTR | 支持     | 等价于SQLSetStmtAttr的SQL_ATTR_ROWS_FETCHED_PTR |

**IPD**

| 字段属性 | FieldIdentifier         | 支持情况 | 值说明                                                       |
| -------- | ----------------------- | -------- | ------------------------------------------------------------ |
| record   | SQL_DESC_DATA_PTR       | 不支持   | 不是用于设置该字段，唯一作用是触发一致性检查                 |
| record   | SQL_DESC_PARAMETER_TYPE | 支持     | 仅支持SQL_PARAM_INPUT和SQL_PARAM_OUTPUT<br />默认值：SQL_PARAM_INPUT |

