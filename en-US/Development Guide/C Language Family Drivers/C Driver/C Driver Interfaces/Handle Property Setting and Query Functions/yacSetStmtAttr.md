## Functionality Introduction

The yacSetStmtAttr function is used to set the value of a certain attribute in the SQL statement handle. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

Statement-level parameters will affect the behavior of the current SQL statement handle. Please configure and retrieve them using yacSetStmtAttr() and [yacGetStmtAttr](./yacGetStmtAttr) function.

|Parameter |Value Data Type |Attribute Read/Write Privilege (R/W) |Description |
| ------------------------------ | ----------------- | ------------------- |------------------------------------------|
| YAC_ATTR_PARAMSET_SIZE          | YacUint32       | R/W                                  | Parameter set size. Sets the number of parameter sets for batch operations. Maximum value is 65535. |
| YAC_ATTR_ROWSET_SIZE            | YacUint32       | R/W                                  | Rowset size. Sets the number of rows to fetch in each fetch operation.                 |
| YAC_ATTR_IS_BATCH_ERRORS        | YacBool         | R/W                                  | Batch error mode. Whether to continue execution when errors occur during batch operations. |
| YAC_ATTR_IS_BATCHROWS           | YacBool         | R/W                                  | Batch row mode. Whether to enable batch row processing. |
| YAC_ATTR_IS_BULK_LOAD           | YacBool         | R/W                                  | Bulk load mode. Whether to enable bulk load for efficient batch insert. |
| YAC_ATTR_IS_DEDUP               | YacBool         | R/W                                  | Deduplication mode. Whether to perform data deduplication. |
| YAC_ATTR_GET_DATA_SUPPORT       | YacBool         | R/W                                  | Get data support. Whether to allow using yacGetData to fetch data (cannot be enabled if there are already fetched rows). |
| YAC_ATTR_ROWS_FETCHED           | YacUint64       | R                                    | Rows fetched. Read-only attribute. Returns the total number of rows already fetched from the current result set.                    |
| YAC_ATTR_ROWS_AFFECTED          | YacUint64       | R                                    | Rows affected. Read-only attribute. Returns the number of result rows affected by the current execution.                         |
| YAC_ATTR_CURSOR_EOF             | YacBool         | R                                    | Cursor EOF flag. Read-only attribute. Returns whether the current result set has been fully fetched.                    |
| YAC_ATTR_IS_DSTB_PART           | YacBool         | R/W                                  | DSTB partition mode. Whether to enable distributed partition processing. |
| YAC_ATTR_IS_SINGLE_PART         | YacBool         | R/W                                  | Single partition mode. Whether to restrict operations to execute within a single partition. |
| YAC_ATTR_ROWS_STATUS            | YacRowStatus[]  | R/W                                  | Row status array pointer. Points to the array of structures that store the execution status of each row. |
| YAC_ATTR_PART_NUM               | YacUint64       | R/W                                  | Partition number. Specifies the target partition number for the operation. |
| YAC_ATTR_TIMEOUT                | YacUint64       | R/W                                  | Timeout (milliseconds). Sets the maximum waiting time for statement execution. Must be >= 0.    |
| YAC_ATTR_SQLTYPE                | YacSQLType      | R                                    | SQL statement type. Read-only attribute. Returns the type of the current SQL statement.                        |
| YAC_ATTR_IMPLICIT_RESULT_COUNT  | YacUint32       | R                                    | Implicit result set count. Read-only attribute. Returns the number of implicit result sets available from the current execution.                      |

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
| bufLength (IN)   | The buffer length of the attribute value to be set. |