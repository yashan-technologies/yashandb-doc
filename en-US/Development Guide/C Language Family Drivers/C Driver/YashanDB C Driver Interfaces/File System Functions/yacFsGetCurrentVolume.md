## Function Overview

The yacFsGetCurrentVolume function is used to get the volume name used by the current connection.

## Function Declaration

```c
YacResult yacFsGetCurrentVolume (YacHandle hConn,
                                 YacChar*  name,
                                 YacUint32 bufSize);
```

## Parameters

|  Parameter| Description|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (OUT)        | Buffer to store the current volume name.                     |
| bufSize (IN)      | Buffer size.                                                |

