## Functionality Overview

The OCILobCharSetId function is used to retrieve the character set ID of a LOB.

## Function Declaration

```c
sword OCILobCharSetId ( OCIEnv                  *envhp, 
                        OCIError                *errhp, 
                        const OCILobLocator     *locp, 
                        ub2                     *csid );
```

## Parameter Description

|Parameter Name |Description |
| ----------- |-----------------------------------------------------------------------|
| envhp (IN/OUT) | Environment handle.                                                      |
| errhp (IN/OUT) | An error handle for obtaining diagnostic information when an error occurs.|
| locp (IN)     | A unique internal LOB locator that references the LOB.                  |
| csid (OUT)    | The output LOB character set ID, which cannot be NULL.<br />If the input LOB type is BLOB, the csid returns 0 (YCI_INVALID_CHARSET) since binary BLOBs do not have a character set concept. |

Some parameters currently only support the following values:

|Return Parameter |Valid Values |
|-------|-----------------------------------------|
| csid | OCI_UTF16ID<br/>YCI_UTF8ID<br/>YCI_ZHS16GBK<br/>0(YCI_INVALID_CHARSET) |
