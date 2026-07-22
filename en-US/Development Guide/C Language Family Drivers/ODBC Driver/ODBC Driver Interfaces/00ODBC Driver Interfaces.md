This section introduces the current version of the YashanDB ODBC driver.

## Connecting to a Data Source

|Interface Name |Interface Description |
| -------- | ---------------- |
| [SQLAllocHandle](Connecting to a Data Source/SQLAllocHandle) | Obtain environment, connection, statement, or descriptor handles.                  |
| [SQLConnect](Connecting to a Data Source/SQLConnect)        | Connect to a specific driver by data source name, user ID, and password.             |
| [SQLDriverConnect](Connecting to a Data Source/SQLDriverConnect) | Connect to a specific driver via a connection string or request the driver manager and driver to display the user's connection dialog. |

## Retrieving Drivers and Data Sources Information

|Interface Name |Interface Description |
| ---------------- | ------- |
| [SQLDataSources](Retrieving Drivers and Data Sources Information/SQLDataSources) | Returns a list of available data sources.      |
| [SQLGetInfo](Retrieving Drivers and Data Sources Information/SQLGetInfo) | Returns information about a specific driver and data source. |
| [SQLGetFunctions](Retrieving Drivers and Data Sources Information/SQLGetFunctions) | Returns the functions supported by the driver.             |
| [SQLGetTypeInfo](Retrieving Drivers and Data Sources Information/SQLGetTypeInfo)   | Returns information about the data types supported by the data source.    |
| [SQLTables](Retrieving Drivers and Data Sources Information/SQLTables) | Returns the names and list of tables stored in a specific data source.               |
| [SQLColumns](Retrieving Drivers and Data Sources Information/SQLColumns) | Returns a list of column names in the specified table. |

## Setting and Retrieving Driver Properties

|Interface Name |Interface Description |
| ---------------- | ----------------- |
| [SQLSetConnectAttr](Setting and Retrieving Driver Properties/SQLSetConnectAttr) | Set connection attributes.     |
| [SQLGetConnectAttr](Setting and Retrieving Driver Properties/SQLGetConnectAttr) | Return the value of connection attributes. |
| [SQLSetEnvAttr](Setting and Retrieving Driver Properties/SQLSetEnvAttr) | Set environment attributes.     |
| [SQLGetEnvAttr](Setting and Retrieving Driver Properties/SQLGetEnvAttr) | Return the value of environment attributes. |
| [SQLSetStmtAttr](Setting and Retrieving Driver Properties/SQLSetStmtAttr) | Set statement attributes.     |
| [SQLGetStmtAttr](Setting and Retrieving Driver Properties/SQLGetStmtAttr) | Return the value of statement attributes. |

## Setting and Retrieving Descriptor Fields

|Interface Name |Interface Description |
| ---------------- | ---------------------------- |
| [SQLGetDescField](Setting and Retrieving Descriptor Fields/SQLGetDescField) | Return the value of a single descriptor field.       |
| [SQLSetDescField](Setting and Retrieving Descriptor Fields/SQLSetDescField) | Set a single descriptor field.           |

## Preparing SQL Requests

|Interface Name |Interface Description |
| ---------------- | ----------------------------- |
| [SQLPrepare](Preparing SQL Requests/SQLPrepare)            | Prepare an SQL statement for later execution. |
| [SQLBindParameter](Preparing SQL Requests/SQLBindParameter) | Allocate storage for parameters in the SQL statement.    |

## Executing Requests

|Interface Name |Interface Description |
| ---------------- | ----------------------------- |
| [SQLExecute](Executing Requests/SQLExecute)            | Execute the prepared statement.                  |
| [SQLExecDirect](Executing Requests/SQLExecDirect)      | Execute the statement.             |
| [SQLNumParams](Executing Requests/SQLNumParams)        | Return the number of parameters in the statement.        |
| [SQLParamData](Executing Requests/SQLParamData) | Used with SQLPutData to provide parameter data during execution. |
| [SQLPutData](Executing Requests/SQLPutData) | Send part or all of the parameter's data value. |

## Retrieving Results and Related Information

|Interface Name |Interface Description |
| ---------------- | ----------------------------- |
| [SQLRowCount](Retrieving Results and Related Information/SQLRowCount) | Return the number of rows affected by insert, update, or delete requests.              |
| [SQLNumResultCols](Retrieving Results and Related Information/SQLNumResultCols) | Return the number of columns in the result set.          |
| [SQLDescribeCol](Retrieving Results and Related Information/SQLDescribeCol) | Describe the columns in the result set.       |
| [SQLColAttribute](Retrieving Results and Related Information/SQLColAttribute) | Describe the attributes of the columns in the result set.        |
| [SQLBindCol](Retrieving Results and Related Information/SQLBindCol) | Allocate storage for the result column and specify data type.       |
| [SQLFetch](Retrieving Results and Related Information/SQLFetch) | Return multiple result rows.       |
| [SQLFetchScroll](Retrieving Results and Related Information/SQLFetchScroll) | Return scrollable result rows. |
| [SQLGetData](Retrieving Results and Related Information/SQLGetData)   | Return part or all of a column in a row of the result set. |
| [SQLGetDiagField](Retrieving Results and Related Information/SQLGetDiagField) | Return the current value of a single field of the diagnostic data structure. |
| [SQLGetDiagRec](Retrieving Results and Related Information/SQLGetDiagRec) | Return the current values of multiple fields of the diagnostic data structure. |

## Terminating Statements

|Interface Name |Interface Description |
| ---------------- | ----------------------------- |
| [SQLFreeStmt](Terminating Statements/SQLFreeStmt) | End statement processing, discard pending results, and free all resources associated with the statement handle. |
| [SQLCloseCursor](Terminating Statements/SQLCloseCursor)    | Close the cursor opened on the statement handle.         |
| [SQLCancel](Terminating Statements/SQLCancel)              | Cancel the processing of the statement.             |
| [SQLEndTran](Terminating Statements/SQLEndTran)            | Commit or roll back the transaction.       |

## Terminating Connections

|Interface Name |Interface Description |
| ---------------- | ----------------------------- |
| [SQLDisconnect](Terminating Connections/SQLDisconnect)     | Close the connection.             |
| [SQLFreeHandle](Terminating Connections/SQLFreeHandle) | Free environment, connection, statement, or descriptor handles.                  |