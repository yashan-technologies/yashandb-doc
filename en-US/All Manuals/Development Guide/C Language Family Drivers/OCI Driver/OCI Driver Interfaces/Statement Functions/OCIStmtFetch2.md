## Functionality Overview

The OCIStmtFetch2 function is used to fetch row information from a query.

## Function Declaration

```c
sword OCIStmtFetch2 ( OCIStmt     *stmthp,
                      OCIError    *errhp, 
                      ub4          nrows,
                      ub2          orientation,
                      sb4          fetchOffset,
                      ub4          mode );
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------------------------------------------ |
| stmthp (IN)       | Statement handle.                                          |
| errhp (IN)        | An error handle to retrieve diagnostic information when an error occurs. |
| nrows (IN)        | The number of rows to fetch from the current position.    |
| orientation (IN)  | Default value is OCI_FETCH_NEXT; setting to OCI_DEFAULT has the same effect as OCI_FETCH_NEXT. |
| fetchOffset (IN)  | An offset used with the orientation parameter to change the position of the current row. This parameter is only effective when the orientation value is OCI_FETCH_ABSOLUTE or OCI_FETCH_RELATIVE. |
| mode (IN)         | Optional mode.                                           |

Some parameters currently support the following values:

|Parameter |Valid Values |
| ----------- |----------------------------|
| orientation | OCI_FETCH_NEXT，OCI_DEFAULT |
| fetchOffset | 0                          |
| mode        | OCI_DEFAULT                |
