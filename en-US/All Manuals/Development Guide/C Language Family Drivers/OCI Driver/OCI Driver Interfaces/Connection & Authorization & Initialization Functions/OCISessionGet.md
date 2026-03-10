## Functionality Overview

The OCISessionGet function is used to obtain and initiate a user session for a specified server.

## Function Declaration

```c
sword OCISessionGet ( OCIEnv            *envhp,
                      OCIError          *errhp,
                      OCISvcCtx        **svchp,
                      OCIAuthInfo       *authInfop,
                      OraText           *dbName,
                      ub4                dbName_len,
                      const OraText     *tagInfo,
                      ub4                tagInfo_len,
                      OraText          **retTagInfo,
                      ub4               *retTagInfo_len,
                      boolean           *found,
                      ub4                mode );
```

## Parameter Description

|Parameter Name |Description |
| ------------------- | ------------------------------------------------------------ |
| envhp (IN/OUT)      | Environment handle.                                          |
| errhp (IN/OUT)      | An error handle<br />When an error occurs, it can be passed to [OCIErrorGet](../Auxiliary Functionality Functions/OCIErrorGet) to retrieve diagnostic information. |
| srvhp (OUT)         | Service context handle.                                      |
| authInfop (IN)      | Authentication handle.                                       |
| dbName (IN)         | Specifies the database server to be used.<br />This parameter points to a specified connection string or service point string. |
| dbName_len (IN)     | Length of dbName.                                           |
| tagInfo (IN)        | Session type, reserved parameter.                            |
| tagInfo_len (IN)    | Length of session type, reserved parameter.                 |
| retTagInfo (OUT)    | Obtained session type, reserved parameter.                  |
| retTagInfo_len (OUT)| Length of obtained session type, reserved parameter.        |
| found (OUT)         | Indicates whether found.                                     |
| mode (IN)           | Mode.                                                       |

Some parameters currently only support the following values:

|Parameter |Valid Values |
|------------|------------------------------------|
| dbName         | Non-NULL                               |
| dbName_len     | Non-0                                  |
| mode       | OCI_DEFAULT<br />OCI_SESSGET_SPOOL |
