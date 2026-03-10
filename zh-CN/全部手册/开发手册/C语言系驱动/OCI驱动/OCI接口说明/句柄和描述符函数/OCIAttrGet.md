## 功能简介

OCIAttrGet函数用于获取当前指向OCI句柄的属性。

## 函数声明

```c
sword OCIAttrGet ( const void     *trgthndlp,
                   ub4             trghndltyp,
                   void           *attributep,
                   ub4            *sizep,
                   ub4             attrtype,
                   OCIError       *errhp );
```

## 参数说明

|  参数名| 说明|
| ---------------- | ---------------------------------------------------------- |
| trgthndlp (IN)   | 指向句柄类型的指针。<br />实际句柄可以是语句句柄、会话句柄等。 |
| trghndltyp (IN)  | 设置属性的句柄类型。                                         |
| attributep (OUT) | 指向属性值存储区的指针。                                     |
| sizep (OUT)      | 属性值的大小。                                               |
| attrtype (IN)    | 正在检索的属性的类型。                                       |
| errhp (IN/OUT)   | 一个错误句柄，当出现错误时可以获取诊断信息。                 |

trghndltyp和attrtype参数目前仅支持以下组合值：

|  trghndltyp有效值| attrtype有效值| 备注|
| ---------------- | ------------ |-------------------------------------------------------------------------------------------------------------------------|
| OCI_HTYPE_STMT | OCI_ATTR_PARAM_COUNT | -                                                                                                                       |
| OCI_HTYPE_STMT | OCI_ATTR_ROW_COUNT | OCI_ATTR_ROW_COUNT属性在INSERT、UPDATE和DELETE语句中，返回最近语句处理的行数，默认值为1。<br />OCI_ATTR_ROW_COUNT属性在其他语句中，则返回SELECT语句后到目前为止处理的行数。 |
| OCI_HTYPE_STMT | OCI_ATTR_ROWS_FETCHED | -                                                                                                                       |
| OCI_HTYPE_STMT | OCI_ATTR_STMT_TYPE | -                                                                                                                       |
| OCI_HTYPE_STMT | OCI_ATTR_IMPLICIT_RESULT_COUNT | 目前返回0。                                                                                                                  |
| OCI_HTYPE_STMT | OCI_ATTR_STATEMENT | -                                                                                                                       |
| OCI_HTYPE_STMT | OCI_ATTR_PARSE_ERROR_OFFSET | 目前返回0。                                                                                                                  |
| OCI_HTYPE_STMT | OCI_ATTR_NUM_DML_ERRORS | 返回批量DML操作的异常错误个数。                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_NAME | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_DATA_TYPE | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_DATA_SIZE | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_PRECISION | OCI_ATTR_PRECISION属性仅对OCI_TYPECODE_NUMBER、OCI_TYPECODE_INTERVAL_DS和OCI_TYPECODE_INTERVAL_YM类型生效。                        |
| OCI_DTYPE_PARAM | OCI_ATTR_SCALE | OCI_ATTR_SCALE属性仅对OCI_TYPECODE_NUMBER和OCI_TYPECODE_INTERVAL_DS类型生效。                                                     |
| OCI_DTYPE_PARAM | OCI_ATTR_CHAR_USED | OCI_ATTR_CHAR_USED需为以字符长定义的字符串类型。                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_CHAR_SIZE | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_CHARSET_FORM | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_CHARSET_ID | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_NUM_COLS | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_LIST_COLUMNS | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_IS_NULL | -                                                                                                                       |
| OCI_HTYPE_DESCRIBE | OCI_ATTR_PARAM | -                                                                                                                       |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SERVER | -                                                                                                                       |
| OCI_HTYPE_SVCCTX | OCI_ATTR_SESSION | -                                                                                                                       |
| OCI_HTYPE_ERROR | OCI_ATTR_DML_ROW_OFFSET | -                                                                                                                       |
