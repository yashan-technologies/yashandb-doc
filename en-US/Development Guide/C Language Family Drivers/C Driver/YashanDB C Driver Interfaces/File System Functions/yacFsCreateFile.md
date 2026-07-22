## Function Overview

The yacFsCreateFile function is used to create a specified file under the current volume.

## Function Declaration

```c
YacResult yacFsCreateFile (YacHandle hConn,
                           YacChar*  name);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | New file name (path). Name length limit is 255 bytes.            |
