## Functionality Overview

OCIStmtPrepare2 is an extended version of OCIStmtPrepare function, used to prepare SQL or PL statements for execution. The main difference from OCIStmtPrepare is the support for statement cache functionality and independent statement handle management.

## Function Declaration

```c
sword OCIStmtPrepare2 ( OCISvcCtx      *svchp,
                        OCIStmt       **stmthp,
                        OCIError       *errhp,
                        const OraText  *stmttext,
                        ub4             stmt_len,
                        const OraText  *key,
                        ub4             keylen,
                        ub4             language,
                        ub4             mode );
```

## Parameter Description

|Parameter Name |Description |
| ------------- | ------------------------------------------------------------ |
| svchp (IN)     | Service context handle.                                   |
| stmthp (OUT)   | Returns the statement handle.                             |
| errhp (IN)     | An error handle to obtain diagnostic information when errors occur. |
| stmttext (IN)  | The SQL or PL statement to be executed.                  |
| stmt_len (IN)  | The length of the statement, measured in characters or bytes, depending on encoding. |
| key (IN)       | Used only for searching the statement in the statement buffer. If key information is provided, the statement text and other parameters will be ignored, and search will be based only on the key. |
| keylen (IN)    | The length of the key.                                   |
| language (IN)  | Language parsing method.                                  |
| mode (IN)      | Optional mode.                                           |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|-----------|----------------|
| stmt        | Non-NULL             |
| stmt_len    | Non-0                |
| key       | NULL           |
| keylen    | 0              |
| language  | OCI_NTV_SYNTAX |
| mode      | OCI_DEFAULT    |
