## Functionality Introduction

The OCIErrorGet function is used to return error messages from the cache and error codes from the YashanDB database.

## Function Declaration

```c
sword OCIErrorGet ( void       *hndlp, 
                    ub4         recordno,
                    OraText    *sqlstate,
                    sb4        *errcodep, 
                    OraText    *bufp,
                    ub4         bufsiz,
                    ub4         type );
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------------------- |
| hndlp (IN)       | Error handle.                                  |
| recordno (IN)    | Indicates the state record from which the application is to retrieve information. |
| sqlstate (OUT)   | SQL state.                                     |
| errcodep (OUT)   | Returned error code.                           |
| bufp (OUT)       | Returned error message text.                   |
| bufsiz (IN)      | Size of the cache provided for the error message (in bytes). |
| type (IN)        | Handle type.                                   |

Some parameters currently support only the following values:

|Parameter |Valid Values |
| -------- | --------------- |
| type     | OCI_HTYPE_ERROR |
| recordno | 1               |
