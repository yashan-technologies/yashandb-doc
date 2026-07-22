## Function Overview

The yacFsMkDir function is used to create a specified directory under the current volume.

## Function Declaration

```c
YacResult yacFsMkDir (YacHandle hConn,
                      YacChar*  name);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | New directory name (path). Name length limit is 255 bytes.       |
