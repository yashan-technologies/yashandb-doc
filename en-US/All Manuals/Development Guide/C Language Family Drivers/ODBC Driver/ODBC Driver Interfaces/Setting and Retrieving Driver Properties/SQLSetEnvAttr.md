## Functionality Overview

Set environment attributes.

## Function Declaration

```c
SQLRETURN SQLSetEnvAttr(  
     SQLHENV        EnvironmentHandle,  
     SQLINTEGER     Attribute,  
     SQLPOINTER     ValuePtr,  
     SQLINTEGER *   StringLength);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------------------------------ |
| EnvironmentHandle (IN)   | The environment handle.                                     |
| Attribute (IN)           | The attribute to be set.                                   |
| ValuePtr (IN)            | A pointer to the value to be associated with the Attribute. |
| StringLength (IN)        | If ValuePtr points to a string or binary cache, this parameter should be the length of *ValuePtr. |

Support for Attribute parameters:

|Attribute |Description |Support Status |
| -------------------------------------- | ------------------------------------------------------------ | -------- |
| SQL_ATTR_ODBC_VERSION (ODBC 3.0)        | This value determines the ODBC version supported.<br />SQL_OV_ODBC3_80 = The driver manager and drivers exhibit ODBC 3.8 behavior.<br />SQL_OV_ODBC3 = The driver manager and drivers exhibit ODBC *3.x* behavior.<br />SQL_OV_ODBC2 = The driver manager and drivers exhibit the following ODBC *2.x* behavior.<br />This function has no default value and this environment attribute must be set before any function of SQLHENV parameter.<br />Currently, only SQL_OV_ODBC3 is supported. | Supported      |
| SQL_ATTR_OUTPUT_NTS (ODBC 3.0)          | This value determines the string return standard.<br />SQL_TRUE = The driver will return string data null terminated. (Default)<br />SQL_FALSE = The driver will not return string data null terminated.<br />Currently, only SQL_TRUE is supported. | Supported      |
| SQL_ATTR_CONNECTION_POOLING (ODBC 3.8)  | This value determines how connection pooling is enabled or disabled at the environment level.<br />SQL_CP_OFF = Connection pool is closed. (Default)<br />SQL_CP_ONE_PER_DRIVER = Each driver supports a single connection pool. Each connection in the pool is associated with a driver.<br />SQL_CP_ONE_PER_HENV = Each environment supports a single connection pool. Each connection in the pool is associated with an environment.<br />SQL_CP_DRIVER_AWARE = Uses the connection pooling aware functionality of the driver (if available). | Not Supported  |
| SQL_ATTR_CP_MATCH (ODBC 3.0)            | This value determines how to select connections from the connection pool.<br />SQL_CP_STRICT_MATCH = Only reuse connections that exactly match the connection options in the call and the connection attributes set by the application. (Default)<br />SQL_CP_RELAXED_MATCH = Connections can be used that have matching connection string keywords. The keywords must match, but not all connection attributes need to match. | Not Supported  |