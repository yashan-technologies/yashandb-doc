## Function Overview

The yacFsSetCurrentVolume function is used to set the volume used by the current connection. After setting, subsequent file operations (creation, deletion, reading, writing, etc.) are performed under this volume.

## Function Declaration

```c
YacResult yacFsSetCurrentVolume (YacHandle hConn,
                                 YacChar*  name);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target volume name (must be an existing volume).                 |
