## Functionality Introduction

The yacGetData function is used by the client to retrieve data from the buffered result set. It returns YAC_SUCCESS to indicate successful retrieval and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacGetData(YacHandle hStmt,
                     YacUint16 id,
                     YacUint32 rowNumber,
                     YacUint32 extType,
                     YacPointer value,
                     YacInt32 bufLen,
                     YacInt32* indicator);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| hStmt (IN/OUT)     | SQL information handle.                                     |
| id (IN)            | Column index, 0 indicates the first column.                |
| rowNumber (IN)     | Row number of the buffered result set to retrieve, starting from 1. |
| type (IN)          | Column index of the buffered result set to retrieve.       |
| value (IN)         | Cache to retrieve from the buffered result set.            |
| bufLen (IN)        | Width of the cache to retrieve from the buffered result set. |
| indicator (OUT)    | Auxiliary pointer, its value indicates the retrieval status. A value of YAC_NULL_DATA indicates that the retrieved data is NULL, while other values indicate the actual size of the retrieved value. <br/>When assigned a value of NULL, it means this pointer is not used. |