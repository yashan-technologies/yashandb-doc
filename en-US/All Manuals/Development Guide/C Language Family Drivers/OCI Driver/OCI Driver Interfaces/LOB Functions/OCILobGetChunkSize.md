## Functionality Overview

The OCILobGetChunkSize function is used to retrieve the chunk size of a LOB.

## Function Declaration

```c
sword OCILobGetChunkSize ( OCISvcCtx       *svchp,
                           OCIError        *errhp,
                           OCILobLocator   *locp,
                           ub4             *chunk_size );
```

## Parameter Description

|Parameter Name |Description |
| ----------- |--------------------------------------------------------------------------------|
| svchp (IN)       | Service context handle.                                                            |
| errhp (IN/OUT)   | An error handle used to obtain diagnostic information when an error occurs.        |
| locp (IN/OUT)    | An internal LOB locator that uniquely references the LOB.                         |
| chunk_size (OUT) | A pointer to store the chunk size of the internal LOB value, which is the suggested size in bytes for reading or writing the LOB value. |