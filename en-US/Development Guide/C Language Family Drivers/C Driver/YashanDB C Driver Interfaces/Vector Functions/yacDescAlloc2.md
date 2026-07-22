## Functionality Overview

The yacDescAlloc2 function is used to allocate space for descriptors, supporting the allocation of vector descriptors (YAC_DESC_VECTOR) and LOB descriptors (YAC_DESC_LOB). Returning YAC_SUCCESS indicates that the space allocation was successful, while returning YAC_ERROR indicates that the space allocation failed.

## Function Declaration

```c
YacResult yacDescAlloc2(YacHandle hEnv,
                        YacVoid** desc,
                        YacDescType type);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hEnv (IN/OUT)   | A handle that records various information related to the environment. |
| desc (OUT)       | A pointer to the descriptor that has been allocated space, which can be yacVector or yacLobLocator. |
| type (IN)        | The type of the descriptor to be allocated, optional values are YAC_DESC_VECTOR and YAC_DESC_LOB. |
