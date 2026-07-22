## Functionality Introduction

The OCIAttrGet function is used to retrieve the attributes of the currently pointed OCI handle.

## Function Declaration

```c
sword OCIAttrGet ( const void     *trgthndlp,
                   ub4             trghndltyp,
                   void           *attributep,
                   ub4            *sizep,
                   ub4             attrtype,
                   OCIError       *errhp );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ---------------------------------------------------------- |
| trgthndlp (IN)    | A pointer to the handle type. <br /> The actual handle can be a statement handle, session handle, etc. |
| trghndltyp (IN)   | Specifies the type of handle for the attribute.         |
| attributep (OUT)  | A pointer to the storage area for the attribute value.  |
| sizep (OUT)       | The size of the attribute value.                         |
| attrtype (IN)     | The type of the attribute being retrieved.              |
| errhp (IN/OUT)    | An error handle used to obtain diagnostic information when an error occurs. |

The trghndltyp and attrtype parameters currently only support the following combinations:

|Valid Values of trghndltyp |Valid Values of attrtype |Remarks |
| ---------------- | ------------ |-------------------------------------------------------------------------------------------------------------------------|
| OCI_HTYPE_STMT | OCI_ATTR_PARAM_COUNT | -                                                                                                                       |
| OCI_HTYPE_STMT                | OCI_ATTR_ROW_COUNT                       | The OCI_ATTR_ROW_COUNT attribute returns the number of rows processed by the most recent statement for INSERT, UPDATE, and DELETE statements. The default value is 1. <br /> For other statements, it returns the number of rows processed so far after a SELECT statement. |
| OCI_HTYPE_STMT | OCI_ATTR_ROWS_FETCHED | -                                                                                                                       |
| OCI_HTYPE_STMT | OCI_ATTR_STMT_TYPE | -                                                                                                                       |
| OCI_HTYPE_STMT                | OCI_ATTR_IMPLICIT_RESULT_COUNT           | Currently returns 0.                                                                                          |
| OCI_HTYPE_STMT | OCI_ATTR_STATEMENT | -                                                                                                                       |
| OCI_HTYPE_STMT                | OCI_ATTR_PARSE_ERROR_OFFSET              | Currently returns 0.                                                                                          |
| OCI_HTYPE_STMT                | OCI_ATTR_NUM_DML_ERRORS                  | Returns the number of exceptions that occurred during a bulk DML operation.                                   |
| OCI_DTYPE_PARAM | OCI_ATTR_NAME | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_DATA_TYPE | -                                                                                                                       |
| OCI_DTYPE_PARAM | OCI_ATTR_DATA_SIZE | -                                                                                                                       |
| OCI_DTYPE_PARAM                | OCI_ATTR_PRECISION                       | The OCI_ATTR_PRECISION attribute is effective only for OCI_TYPECODE_NUMBER, OCI_TYPECODE_INTERVAL_DS, and OCI_TYPECODE_INTERVAL_YM types. |
| OCI_DTYPE_PARAM                | OCI_ATTR_SCALE                           | The OCI_ATTR_SCALE attribute is effective only for OCI_TYPECODE_NUMBER and OCI_TYPECODE_INTERVAL_DS types.   |
| OCI_DTYPE_PARAM                | OCI_ATTR_CHAR_USED                       | The OCI_ATTR_CHAR_USED must be defined for string types defined in character length.                          |
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
