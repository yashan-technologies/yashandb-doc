## Functionality Introduction

The OCITransStart function is used to start or resume a transaction.

## Function Declaration

```c
sword OCITransStart ( OCISvcCtx    *svchp, 
                      OCIError     *errhp, 
                      uword        timeout,
                      ub4          flags);
```

## Parameter Description

|Parameter Name |Description |
|--------------|------------------------|
| svchp (IN)       | Service context handle.                 |
| errhp (IN)       | An error handle to obtain diagnostic information when an error occurs. |
| timeout (IN)     | Timeout period (in seconds).            |
| flags (IN)       | Specifies whether to start a new transaction or resume an existing one. |

Some parameters currently support the following values:

|Parameter |Valid Values |
|--------|------------------------------------|
| flags  | TMNOFLAGS<br/>TMJOIN<br/>TMRESUME  |
