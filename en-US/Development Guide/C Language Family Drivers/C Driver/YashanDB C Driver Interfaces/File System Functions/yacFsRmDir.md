## Function Overview

The yacFsRmDir function is used to delete a specified directory under the current volume.

## Function Declaration

```c
YacResult yacFsRmDir (YacHandle hConn,
                      YacChar*  name,
                      YacBool   cascade);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target directory name (path, must be an existing directory) to delete. |
| cascade (IN)      | Whether to delete recursively.<br>YAC_TRUE: Recursively delete the directory and all its subdirectories and files.<br>YAC_FALSE: Only delete empty directories. |
