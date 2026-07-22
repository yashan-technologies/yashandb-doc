## Function Overview

The yacFsFileExist function is used to check whether a specified file exists under the current volume.

## Function Declaration

```c
YacResult yacFsFileExist (YacHandle hConn,
                          YacChar*  name,
                          YacBool*  exist);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target file name (path, must be an existing file).                |
| exist (OUT)       | Whether the file exists.<br>YAC_TRUE: File exists.<br>YAC_FALSE: File does not exist. |
