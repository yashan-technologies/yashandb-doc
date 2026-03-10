## Functionality Introduction

The yacBindParameterByName function is used for client-side binding of specified parameter attributes by parameter name. It returns YAC_SUCCESS to indicate successful binding and YAC_ERROR to indicate binding failure.

## Function Declaration

```c
yacResult yacBindParameterByName ( yacHandle  hStmt,
                                   YacChar*   name, 
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
| hStmt (IN/OUT)       | Handle that records various information related to SQL.      |
| name (IN)            | Parameter name.                                             |
| direction (IN)       | Parameter type, 1 for input parameter, 2 for output parameter, 3 for both input and output. |
| bindType (IN)        | Type that needs to be bound to the parameter.                |
| value (IN)           | Value that needs to be bound to the parameter.               |
| bindSize (IN)        | Maximum length for the value to be bound to the parameter.   |
| bufLength (IN)       | Length of the cache where the value to be bound resides.     |
| indicator (IN/OUT)   | Auxiliary pointer.<br/>As input: indicates the length of the variable-length value bound to the ID column; does not apply to fixed-length types (including types bound using loblocator). When assigned YAC_NULL_DATA, it indicates that the data to be bound is NULL.<br/>As output: indicates the actual size of the output value; if the parameter value is YAC_NULL_DATA, it indicates that the retrieved data is NULL. |