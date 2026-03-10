## Functionality Introduction

The yacAllocHandle function is used to allocate space for a handle. It returns YAC_SUCCESS to indicate successful allocation and YAC_ERROR to indicate failure in allocation.

The order of space allocation must follow the [handle hierarchy](./00句柄和描述符函数), with the upper-level handle serving as the input handle when allocating space for this handle. 

## Function Declaration

```c
yacResult yacAllocHandle ( yacHandleType type,
                           yacHandle     input,
                           yacHandle*    output);
```

## Parameter Description

|Parameter Name |Description |
| ------------ | -------------------------------- |
| type (IN)      | The type of the handle. Contains the following values:<br/>YAC_HANDLE_ENV: Global environment handle<br/>YAC_HANDLE_DBC: Database connection handle<br/>YAC_HANDLE_STMT: SQL statement handle |
| input (IN)     | The input handle. When type is YAC_HANDLE_ENV, this parameter inputs NULL. |
| output (OUT)   | The output handle, which is linked to the input handle. |