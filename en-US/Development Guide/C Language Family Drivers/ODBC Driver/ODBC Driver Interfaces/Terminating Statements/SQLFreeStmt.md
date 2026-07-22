## Functionality Overview

End the statement processing, abandon the pending results, and release all resources associated with the statement handle.

## Function Declaration

```c
SQLRETURN SQLFreeStmt(  
     SQLHSTMT       StatementHandle,  
     SQLUSMALLINT   Option);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ---------- |
| StatementHandle (IN)    | Statement handle.   |
| Option (IN)             | Optional value.     |

Support status of the Option parameter:

|Option |Description |Support Status |
| ---------------- | ------------------------------------------------------------ | -------- |
| SQL_CLOSE        | Same as SQLCloseCursor                                             | Supported       |
| SQL_UNBIND       | Sets the SQL_DESC_COUNT field of ARD to 0, releases all column caches bound by SQLBindCol for the given StatementHandle. | Supported       |
| SQL_RESET_PARAMS  | Sets the SQL_DESC_COUNT field of APD to 0, releases all parameter caches set by SQLBindParameter for the given StatementHandle. | Supported       |
| SQL_DROP         | Deprecated                                                         | Not supported    |