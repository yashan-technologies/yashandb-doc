## Functionality Introduction

The OCIServerAttach function is used to create an access path to a data source.

## Function Declaration

```c
sword OCIServerAttach ( OCIServer     *srvhp,
                        OCIError      *errhp,
                        const OraText *dblink,
                        sb4            dblink_len,
                        ub4            mode );
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| srvhp (IN/OUT)   | An uninitialized server handle. <br /> This call initializes it. Passing an initialized server handle will cause an error. |
| errhp (IN/OUT)   | An error handle. <br /> It can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to obtain diagnostic information when an error occurs. |
| dblink (IN)      | Specifies the database server to use. <br /> This parameter points to the specified connection string or service name string. |
| dblink_len (IN)  | The length of the string pointed to by dblink.         |
| mode (IN)        | Specifies the operation mode.                           |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|------------|--------------|
| dblink        | Non-NULL       |
| dblink_len    | Non-zero       |
| mode       | OCI_DEFAULT  |
