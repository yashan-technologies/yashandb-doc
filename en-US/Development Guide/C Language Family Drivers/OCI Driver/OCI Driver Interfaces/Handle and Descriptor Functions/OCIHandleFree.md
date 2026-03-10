## Functionality Introduction

The OCIHandleFree function is used to explicitly free handles.

## Function Declaration

```c
sword OCIHandleFree ( void      *hndlp,
                      ub4        type );
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ------------------------------ |
| hndlp (IN)     | The handle allocated by [OCIHandleAlloc](OCIHandleAlloc). |
| type (IN)      | Specifies the type of storage to be freed.          |

Some parameters currently only support the following values:

|Parameter |Valid Values |
| ---- | ------------------------------------------------------------ |
| type | OCI_HTYPE_ERROR<br />OCI_HTYPE_DESCRIBE<br />OCI_HTYPE_SERVER<br />OCI_HTYPE_SESSION<br />OCI_HTYPE_SVCCTX<br />OCI_HTYPE_STMT<br />OCI_HTYPE_ENV<br />OCI_HTYPE_SPOOL |
