## Functionality Introduction

The `yacTextToRowId` function is used to convert a String type into a RowId type. It returns YAC_SUCCESS to indicate a successful conversion and YAC_ERROR to indicate a failure in conversion.

## Function Declaration

```c
YacResult yacTextToRowId(YacChar* str,
                         YacInt32 length,
                         YacRowId* rowId);
```

## Parameter Description

|Parameter Name |Description |
| ----------- | ------------------------------------------ |
| str (IN)      | Address of the string buffer.               |
| length (IN)   | Actual length of the string.                |
| rowId (OUT)   | Pointer of YacRowId type to store the output YacRowId. |