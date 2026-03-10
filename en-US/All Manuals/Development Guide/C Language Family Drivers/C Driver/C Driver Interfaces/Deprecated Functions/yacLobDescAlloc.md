## Functionality Overview

The `yacLobDescAlloc` function is used to allocate space for a loblocator in the LOB protocol. It returns `YAC_SUCCESS` to indicate successful allocation, and `YAC_ERROR` to indicate allocation failure.

> **Note**: 
>
> This function is deprecated. It is recommended to use [yacLobDescAlloc2](../LOB Functions/yacLobDescAlloc2).

## Function Declaration

```c
yacResult yacLobDescAlloc ( yacHandle   hConn,
                            yacType     type,
                            YacVoid**   desc);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hConn (IN/OUT) | A handle that records various connection-related information. |
| type (IN)      | LOB type.                            |
| desc (OUT)     | A pointer to the allocated space for the loblocator. |