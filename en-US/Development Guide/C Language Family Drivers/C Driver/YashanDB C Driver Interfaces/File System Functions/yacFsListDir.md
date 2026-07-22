## Function Overview

The yacFsListDir function is used to list files or directories under a specified directory of the current volume. The list operation uses a callback mechanism and supports batch retrieval of directory contents.

> **Caution**:
>
> - Nested calls to listdir are not allowed on the same connection (calling listdir again within the callback function).
> - The server requires that one iteration be explicitly completed before starting the next iteration.

## Function Declaration

```c
YacResult yacFsListDir (YacHandle         hConn,
                        YacChar*          dir,
                        YacUint32         count,
                        YacIterFsEntry    iter,
                        YacPointer        ctx);
```

## Parameters

|  Parameter| Description|
| --- | --- |
| hConn (IN)        | Connection handle that identifies the connection to the database. |
| dir (IN)          | Target directory name (path, must be an existing directory).      |
| count (IN)        | Maximum number of entries returned per request.                   |
| iter (IN)         | Iteration callback function to process the retrieved directory entries. The function definition is described later. |
| ctx (IN)          | User context pointer, which will be passed to the callback function. |

Callback function type:

```c
typedef YacBool (*YacIterFsEntry) (YacPointer            ctx,
                                   const YacFsEntry*     entries,
                                   YacUint32             count);
```

YacFsEntry structure definition:

|  Field  |  Type  |  Description  |
| --- | --- | --- |
| type    | YacUint8   | Entry type. The type enumeration is described later. |
| name    | YacChar[]  | Entry name.                                            |

YacFsEntryType enumeration definition:

|  Enumeration Value  |  Description  |
| --- | --- |
| YAC_FSENTRY_FILE | Regular file. |
| YAC_FSENTRY_DIR  | Directory.    |
