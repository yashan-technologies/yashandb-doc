## Function Overview

The yacFsFileRead function is used to read file data from a specified position under the current volume. The read operation supports large file chunked transfer.

## Function Declaration

```c
YacResult yacFsFileRead (YacHandle hConn,
                         YacChar*  name,
                         YacUint64 offset,
                         YacUint8* buf,
                         YacUint32 bufLen,
                         YacUint32* read);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target file name (path, must be an existing file).                |
| offset (IN)       | Read starting position (in bytes).                                |
| buf (OUT)         | Buffer to store the read data.                                    |
| bufLen (IN)       | Buffer size.                                                      |
| read (OUT)        | Actual number of bytes read.                                      |
