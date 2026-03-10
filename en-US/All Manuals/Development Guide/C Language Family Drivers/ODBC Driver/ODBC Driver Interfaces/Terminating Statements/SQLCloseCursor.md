## Functionality Overview

Closes the cursor that is open on the statement handle.

The SQLCloseCursor function must be called after SQLFetch to close the currently fetching cursor; otherwise, the driver manager layer will return SQL_ERROR when calling other functions, and it will not be possible to use the diagnostic functions to obtain error information.

## Function Declaration

```c
SQLRETURN SQLCloseCursor(  
     SQLHSTMT     StatementHandle);
```

## Parameter Description

|Parameter Name |Description |
| -------------------- | ---------- |
| StatementHandle (IN)  | The statement handle. |