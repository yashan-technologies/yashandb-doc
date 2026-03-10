## Functionality Overview

The OCIStmtFetch function is used to retrieve row information from a query.

## Function Declaration

```c
sword OCIStmtFetch ( OCIStmt     *stmtp,
                     OCIError    *errhp, 
                     ub4          nrows,
                     ub2          orientation,
                     ub4          mode );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------------------------ |
| stmtp (IN)      | Statement handle.                           |
| errhp (IN)      | An error handle that can provide diagnostic information when an error occurs. |
| nrows (IN)      | The number of rows to fetch from the current position. |
| orientation (IN) | Default value is OCI_FETCH_NEXT.          |
| mode (IN)       | Optional fetching mode.                    |

Some parameters currently support only the following values:

|Parameter |Valid Values |
| ----------- | -------------- |
| orientation | OCI_FETCH_NEXT |
| mode        | OCI_DEFAULT    |
