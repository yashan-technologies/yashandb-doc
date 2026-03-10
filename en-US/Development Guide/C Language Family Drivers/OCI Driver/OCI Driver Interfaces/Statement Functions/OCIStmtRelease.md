## Functionality Introduction

The OCIStmtRelease function is used to release a statement handle.

## Function Declaration

```c
sword OCIStmtRelease ( OCIStmt        *stmthp,  
                       OCIError       *errhp,
                       const OraText  *key,
                       ub4             keylen,
                       ub4             mode );  
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ------------------------------------------ |
| stmthp(IN)     | Statement handle.                             |
| errhp(IN)      | An error handle that provides diagnostic information when an error occurs. |
| key(IN)        | Key value information for the statement, reserved parameter. |
| keylen(IN)     | Length of the key value for the statement, reserved parameter. |
| mode(IN)       | Mode, reserved parameter.                     |

Some parameters currently support the following values:

|Parameter |Valid Values |
| ------ |------------------------------------------|
| mode | OCI_STRLS_CACHE_DELETE<br />OCI_DEFAULT  |
