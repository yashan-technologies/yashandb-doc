## Functionality Overview

Returns the value of environment attributes.

## Function Declaration

```c
SQLRETURN SQLGetEnvAttr(  
     SQLHENV        EnvironmentHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER     BufferLength,  
     SQLINTEGER *   StringLengthPtr);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| EnvironmentHandle (IN)  | The environment handle.                                      |
| Attribute (IN)          | The attribute to retrieve.                                   |
| ValuePtr (OUT)         | A pointer to the cache, where the current value of the feature specified by Attribute is returned. |
| BufferLength (IN)      | If ValuePtr points to a string, this parameter should be the length of *ValuePtr. |
| StringLengthPtr (OUT)   | A pointer to the cache, which will return the total number of bytes. |

Support status of the Attribute parameter:

|Attribute |Description |Support Status |
| --------------------------- | -------------------------------------------- | -------- |
| SQL_ATTR_CONNECTION_POOLING | Returns SQL_CP_OFF                              | Supported      |
| SQL_ATTR_ODBC_VERSION       | Default is SQL_OV_ODBC3, currently only supports SQL_OV_ODBC3 | Supported      |
| SQL_ATTR_OUTPUT_NTS         | Returns SQL_TRUE                                | Supported      |