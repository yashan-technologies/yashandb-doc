## Functionality Introduction

The `yacBindColumn` function is used for binding property values to specified columns on the client side. It returns `YAC_SUCCESS` to indicate that the binding was successful, or `YAC_ERROR` to indicate that the binding failed.

## Function Declaration

```c
yacResult yacBindColumn ( yacHandle  hStmt,
                          yacUint16  id,
                          yacType    type,
                          yacPointer value,
                          yacInt32   bufLen,
                          yacInt32*  indicator);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------------------------------------------ |
| hStmt (IN/OUT)   | A handle that records various information related to SQL.   |
| id (IN)          | The index of the column, where 0 represents the first column. |
| type (IN)        | The type to be bound to the column with the given id.        |
| value (IN)       | The value to be bound to the column with the given id.       |
| bufLen (IN)      | The column width to be bound to the column with the given id. This setting is only effective for variable-length types. |
| indicator (OUT)  | An auxiliary pointer, where its value indicates the result set data retrieval situation. A value of `YAC_NULL_DATA` indicates that the retrieved data is NULL; other values indicate the actual size of the retrieved value.<br/>When the assigned value is NULL, it indicates that this pointer is inactive. |

Currently, the output data cache does not support truncation of variable-length types. When retrieving result set data, any variable-length type that encounters insufficient size in the output data cache will return an error.