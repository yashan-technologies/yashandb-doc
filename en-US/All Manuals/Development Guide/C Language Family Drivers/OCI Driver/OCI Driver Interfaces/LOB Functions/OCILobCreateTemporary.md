## Introduction to Functionality

The OCILobCreateTemporary function is used to create a temporary LOB.

## Function Declaration

```c
sword OCILobCreateTemporary(OCISvcCtx          *svchp,
                            OCIError           *errhp,
                            OCILobLocator      *locp,
                            ub2                 csid,
                            ub1                 csfrm,
                            ub1                 lobtype,
                            boolean             cache,
                            OCIDuration         duration);
```

## Parameter Description

|Parameter Name |Description |
| ----------- |-----------------------------------------------------------------------------------------------------|
| svchp (IN)       | Service context handle.                                             |
| errhp (IN/OUT)   | An error handle that can retrieve diagnostic information when an error occurs.                                               |
| locp (IN/OUT)    | The internal LOB locator that uniquely references the LOB.                                                  |
| csid (IN)        | LOB character set ID.                                                                                                                                                   |
| csfrm (IN)       | The LOB character set form for cache data, default value is SQLCS_IMPLICIT.<br />SQLCS_IMPLICIT indicates the database character set ID, used for creating CLOB.<br />OCI_DEFAULT is used for implicit creation of CLOB.<br />SQLCS_NCHAR indicates the NCHAR character set ID, used for creating NCLOB. |
| lobtype (IN)     | The type of LOB to be created. OCI_TEMP_BLOB is used to create a temporary BLOB, OCI_TEMP_CLOB is used to create a temporary CLOB or NCLOB.                       |
| cache (IN)       | Pass TRUE if the temporary LOB should be read into the buffer; pass FALSE if it should not be read into the buffer. The default value for NOCACHE functionality is FALSE.                                                       |
| duration (IN)    | The duration of the temporary LOB. Valid values are OCI_DURATION_SESSION or OCI_DURATION_CALL.                                                           |

Currently, some parameters only support the following values:

|Parameter |Valid Values |
| ------ |----------------------|
| csid | OCI_DEFAULT          |
| csfrm   | SQLCS_IMPLICIT       |
| cache   | FALSE                |
| duration   | OCI_DURATION_SESSION |
