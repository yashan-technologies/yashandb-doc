## Functionality Overview

The OCIStmtExecute function is used by applications to request the server to execute SQL or PL statements.

## Function Declaration

```c
sword OCIStmtExecute ( OCISvcCtx           *svchp,
                       OCIStmt             *stmtp,
                       OCIError            *errhp,
                       ub4                  iters,
                       ub4                  rowoff,
                       const OCISnapshot   *snap_in,
                       OCISnapshot         *snap_out,
                       ub4                  mode );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------------------------ |
| svchp (IN/OUT)   | Service context handle.                            |
| stmtp (IN/OUT)   | Statement handle.                                  |
| errhp (IN/OUT)   | An error handle, used to retrieve diagnostic information when an error occurs. |
| iters (IN)       | The value of iters.                               |
| rowoff (IN)      | The starting index in the array binding related to this multi-row execution. |
| snap_in (IN)     | Optional parameter.                                |
| snap_out (OUT)   | Optional parameter.                                |
| mode (IN)        | Optional mode.                                     |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| -------- | ------------------------------------------------------------ |
| iters     | Number of rows<br />For DML statements, it is paramsetsize<br />For DQL statements, it is rowsetsize |
| rowoff   | 0                                                            |
| snap_in  | NULL                                                         |
| snap_out | NULL                                                         |
| mode     | OCI_DEFAULT<br />OCI_DESCRIBE_ONLY<br />OCI_COMMIT_ON_SUCCESS |
