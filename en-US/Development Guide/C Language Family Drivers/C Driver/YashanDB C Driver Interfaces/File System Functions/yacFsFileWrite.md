## Function Overview

The yacFsFileWrite function is used to write data to a specified file under the current volume. The write operation uses streaming transmission and supports large file chunked writing.

> **Caution**:
>
> - The write operation overwrites the original file content (truncate mode).
> - When multi-version is not enabled, the original file content will no longer exist.

## Function Declaration

```c
YacResult yacFsFileWrite (YacHandle      hConn,
                          YacChar*       name,
                          YacFsStream*   stream,
                          YacUint32*     wrote);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target file name (path, must be an existing file).                |
| stream (IN)       | Data stream object that provides data to write. The structure definition is described later. |
| wrote (OUT)       | Actual number of bytes written.                                   |

YacFsStream structure definition:

|  Field  |  Type  |  Description  |
| --- | --- | --- |
| ctx       | YacPointer     | Context pointer.                                        |
| buf       | YacUint8*      | Data buffer.                                            |
| bufLen    | YacUint64      | Buffer size.                                            |
| read      | Function pointer | Read callback function, signature: `YacUint32 (*read)(YacFsStream* stream)` |
