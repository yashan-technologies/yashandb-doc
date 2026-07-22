## Function Overview

The yacFsRmFile function is used to delete a specified file under the current volume.

## Function Declaration

```c
YacResult yacFsRmFile (YacHandle hConn,
                       YacChar*  name);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target file name (path, must be an existing file) to delete.     |
