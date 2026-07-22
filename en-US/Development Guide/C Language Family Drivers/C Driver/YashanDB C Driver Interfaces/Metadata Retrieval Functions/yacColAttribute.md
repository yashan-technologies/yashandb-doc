## Functionality Introduction

The yacColAttribute function is used to retrieve a specific column attribute value from an SQL information handle. The detailed attribute value information is shown in the table below. A return value of YAC_SUCCESS indicates successful retrieval, while YAC_ERROR indicates a failure to retrieve.

|Attribute |Value Data Type |Description |
| ------------------------------ | ----------------- | ------------------------ |
| YAC_COL_ATTR_DISPLAY_SIZE      | YacUint32          | Column display size         |
| YAC_COL_ATTR_DISPLAY_CHAR_SIZE | YacUint32          | Column display character size |
| YAC_COL_ATTR_NAME              | YarChar*           | Column name                 |
| YAC_COL_ATTR_SIZE              | YacUint32          | Column size                 |
| YAC_COL_ATTR_CHAR_SIZE         | YacUint32          | Column character size       |
| YAC_COL_ATTR_TYPE              | YacUint8           | Column type                 |
| YAC_COL_ATTR_PRECISION         | YacUint8           | Column precision            |
| YAC_COL_ATTR_SCALE             | YacInt8            | Column scale                |
| YAC_COL_ATTR_NULLABLE          | YacUint8           | Whether the column can be NULL |
| YAC_COL_ATTR_CHAR_USED         | YacUint8           | Whether type definition is character length definition |

## Function Declaration

```c
YacResult yacColAttribute(YacHandle hStmt,
                          YacUint16 id,
                          YacColAttr attr,
                          YacVoid* value,
                          YacInt32 bufLen,
                          YacInt32* stringLength);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ---------------------------- |
| hStmt (IN/OUT)           | SQL information handle.         |
| id (IN)                  | Column index, 0 indicates the first column. |
| attr (IN)                | Attribute type.                |
| value (OUT)              | Stores the retrieved attribute value. |
| bufLen (OUT)             | Length of the storage area for the retrieved attribute value. |
| stringLength (IN/OUT)    | Actual length of the retrieved string. |