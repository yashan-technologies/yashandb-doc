## Function Overview

The yacFsFileStat function is used to get the basic attribute information of a specified file under the current volume.

## Function Declaration

```c
YacResult yacFsFileStat (YacHandle     hConn,
                         YacChar*      name,
                         YacFileStat*  stat);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| name (IN)         | Target file name (path, must be an existing file).                |
| stat (OUT)        | File attribute information. The structure definition is described later. |

YacFileStat structure definition:

|  Field  |  Type  |  Description  |
| --- | --- | --- |
| id            | YacUint64 | File ID.                                               |
| size          | YacUint64 | File size (in bytes).                                   |
| createTime    | YacUint64 | Creation time (timestamp).                              |
| modifyTime    | YacUint64 | Modification time (timestamp).                          |
| type          | YacUint8  | File type, See [YacFsEntryType in yacFsListDir](./yacFsListDir.md#parameters). |
