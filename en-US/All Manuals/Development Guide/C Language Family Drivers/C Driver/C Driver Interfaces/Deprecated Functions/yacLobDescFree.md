## Functionality Introduction

The `yacLobDescFree` function is used to free space for a loblocator in the LOB protocol. It returns `YAC_SUCCESS` to indicate success and `YAC_ERROR` to indicate failure.

> **Note**: 
>
> This function is deprecated. It is recommended to use [yacLobDescFree2](../LOB Functions/yacLobDescFree2).

## Function Declaration

```c
yacResult yacLobDescFree ( YacVoid* desc,
                           yacType  type);
```

## Parameter Description

|Parameter Name |Description |
| --------- | ---------------- |
| desc (IN)     | Pointer to the loblocator. |
| type (IN)     | LOB type.           |