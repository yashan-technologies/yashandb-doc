## Functionality Introduction

The yacBindParameter function is used for binding attribute values to specified parameters on the client side. A return value of YAC_SUCCESS indicates binding success, while YAC_ERROR indicates binding failure.

## Function Declaration

```c
yacResult yacBindParameter ( yacHandle  hStmt,
                             yacUint16  id,
                             YacParamDirection direction,
                             YacType bindType,
                             yacPointer value,
                             YacInt32 bindSize,
                             YacInt32 bufLength,
                             yacInt32*  indicator);
```

## Parameter Description

|Parameter Name |Description |
| ------------------ | ------------------------------------------------------------ |
| hStmt (IN/OUT)      | A handle that records various information related to SQL.    |
| id (IN)             | The index of the parameter, where 1 indicates the first parameter. If the precompiled SQL uses placeholders in the `:name` format with the same name multiple times, multiple bindings are needed when binding values by position. Generally, placeholders with the same name correspond to the binding of the same value by position. |
| direction (IN)      | Parameter type:<br />- YAC_PARAM_INPUT (1): Input parameter<br />- YAC_PARAM_OUTPUT (2): Output parameter<br />- YAC_PARAM_INPUT_OUTPUT (3): Input/Output parameter |
| bindType (IN)       | The type to be bound to this parameter.                      |
| value (IN)          | The value to be bound to this parameter.                    |
| bindSize (IN)       | The length of the cache where the value to be bound is located. |
| bufLength (IN)      | Reserved parameter, currently inactive.                      |
| indicator (IN/OUT)  | Auxiliary pointer.<br/>As input: represents the length of the variable-length value bound to the id column; it does not apply to non-variable-length types (including those bound using loblocator). When assigned YAC_NULL_DATA, it indicates that the data to be bound is NULL.<br/>As output: indicates the status of the retrieved values from the result set.<br/>When assigned NULL, it indicates that the pointer is inactive. |