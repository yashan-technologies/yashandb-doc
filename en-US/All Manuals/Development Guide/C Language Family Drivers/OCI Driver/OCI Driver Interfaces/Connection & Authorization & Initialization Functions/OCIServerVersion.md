## Functionality Introduction

The OCIServerVersion function is used to retrieve the version of the Oracle database, currently fixed to return the Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production version number.

## Function Declaration

```c
sword OCIServerVersion ( void         *hndlp, 
                         OCIError     *errhp, 
                         OraText      *bufp,
                         ub4           bufsz
                         ub1           hndltype );
```

## Parameter Description

|Parameter Name |Description |
| -------------- |---------------------------------------------------|
| hndlp (IN/OUT)   | The handle of the service context.                          |
| errhp (IN/OUT)   | An error handle.<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information. |
| bufp (IN/OUT)    | A cache to return the version information.                  |
| bufsz (IN)       | Length of the cache (in bytes).                             |
| hndltype (IN)    | The handle type of hndlp.                                   |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ----- | -------------- |
| bufp | Oracle Database 19c Enterprise Edition Release 19.0.0.0.0 - Production |
| hndltype  | OCI_HTYPE_SVCCTX |
