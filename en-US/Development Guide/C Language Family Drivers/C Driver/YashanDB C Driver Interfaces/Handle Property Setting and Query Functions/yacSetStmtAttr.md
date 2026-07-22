## Functionality Introduction

The yacSetStmtAttr function is used to set the value of a certain attribute in the SQL statement handle. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

Statement-level parameters will affect the behavior of the current SQL statement handle. Please configure and retrieve them using yacSetStmtAttr() and [yacGetStmtAttr](yacGetStmtAttr) function.

|Parameter |Value Data Type |Attribute Read/Write Privilege (R/W) |Description |
| ------------------------------ | ----------------- | ------------------- |------------------------------------------|
| YAC_ATTR_PARAMSET_SIZE          | YacUint32       | R/W                                  | This value specifies the batch execution row count for the current statement, with a maximum value of 65535. |
| YAC_ATTR_ROWSET_SIZE            | YacUint32       | R/W                                  | This value specifies the number of rows to fetch in this Fetch operation.                 |
| YAC_ATTR_ROWS_FETCHED           | YacUint64       | R                                    | This value returns the total number of rows fetched from the current result set.                    |
| YAC_ATTR_ROWS_AFFECTED          | YacUint64       | R                                    | This value returns the number of result rows affected by the current execution.                         |
| YAC_ATTR_CURSOR_EOF             | YacBool         | R                                    | This value returns whether the current result set has been fully fetched.                    |
| YAC_ATTR_SQLTYPE                | YacSQLType      | R                                    | This value returns the type of SQL statement executed this time.                        |
| YAC_ATTR_IMPLICIT_RESULT_COUNT  | YacUint32       | R                                    | This value returns the number of implicit result sets available from this execution.                      |
| YAC_ATTR_GET_DATA_SUPPORT       | YacBool         | R/W                                  | This value is used to set or query whether the current statement has results buffer enabled for using the yacGetData function. |
| YAC_ATTR_ROWS_STATUS            | YacRowStatus[]  | R/W                                  | This value returns the status of each row of data during batch fetching.                    |
| YAC_ATTR_TIMEOUT                | YacUint64       | R/W                                  | This value sets the SQL execution timeout for the current statement. Unit: seconds; 0 means no timeout is set.    |

## Function Declaration

```c
YacResult yacSetStmtAttr(yacHandle hStmt,
                         yacStmtAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------- |
| hStmt (IN/OUT)   | Connection information handle.       |
| attr (IN)        | Attribute type.                      |
| value (IN)       | The attribute value to be set.      |
| bufLength (IN)   | The cache length of the attribute value to be set. |