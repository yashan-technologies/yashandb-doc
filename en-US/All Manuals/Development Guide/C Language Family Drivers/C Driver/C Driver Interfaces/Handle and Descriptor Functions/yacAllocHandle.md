## Functionality Introduction

The yacAllocHandle function is used to allocate space for a handle. It returns YAC_SUCCESS to indicate successful allocation and YAC_ERROR to indicate failure in allocation.

## Function Declaration

```c
yacResult yacAllocHandle ( yacHandleType type,
                           yacHandle     input,
                           yacHandle*    output);
```

## Parameter Description

|Parameter Name |Description |
| ------------ | -------------------------------- |
| type (IN)      | The type of the handle.         |
| input (IN)     | The input handle.               |
| output (OUT)   | The output handle, which is linked to the input handle. |