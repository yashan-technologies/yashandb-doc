## Functionality Introduction

The yacRowIdToText function is used to convert RowId type to String type. It returns YAC_SUCCESS to indicate a successful conversion and YAC_ERROR to indicate a failure in conversion.

## Function Declaration

```c
YacResult yacRowIdToText(YacRowId* rowId,
                         YacChar* str,
                         YacInt32 bufLength,
                         YacInt32* length);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ---------------------------------- |
| rowId (IN)      | Pointer to YacRowId type.          |
| str (OUT)       | Address of the string buffer.       |
| bufLength (IN)  | Length of the string buffer.        |
| length (OUT)    | Used to retrieve the actual length of the string, can be NULL. |