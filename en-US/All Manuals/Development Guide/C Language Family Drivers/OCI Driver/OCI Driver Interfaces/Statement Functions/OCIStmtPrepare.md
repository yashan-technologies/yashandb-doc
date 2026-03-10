## Functionality Brief

The OCIStmtPrepare function is used to prepare SQL or PL statements for execution.

## Function Declaration

```c
sword OCIStmtPrepare ( OCIStmt       *stmtp,
                       OCIError      *errhp,
                       const OraText *stmt, 
                       ub4            stmt_len,
                       ub4            language,
                       ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------ |
| stmtp (IN)     | Statement handle.                               |
| errhp (IN)     | An error handle that provides diagnostic information when an error occurs. |
| stmt (IN)      | The SQL or PL statement to be executed.        |
| stmt_len (IN)  | The length of the statement, represented in characters or bytes, depending on the encoding. |
| language (IN)  | Language parsing method.                        |
| mode (IN)      | Optional mode.                                 |

Some parameters currently support only the following values:

|Parameter |Valid Values |
| -------- |----------------|
| stmt      | Non-NULL          |
| stmt_len  | Non-0             |
| language | OCI_NTV_SYNTAX |
| mode     | OCI_DEFAULT    |
