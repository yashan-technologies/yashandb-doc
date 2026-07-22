## Functionality Overview

The yacDescFree2 function is used to free space for descriptors, supporting the freeing of vector descriptors (YAC_DESC_VECTOR) and LOB descriptors (YAC_DESC_LOB). Returning YAC_SUCCESS indicates that the space was freed successfully, while returning YAC_ERROR indicates that the space freeing failed.

## Function Declaration

```c
YacResult yacDescFree2(YacHandle hEnv,
                       YacVoid** desc,
                       YacDescType type);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hEnv (IN/OUT)   | A handle that records various information related to the environment. |
| desc (OUT)       | A pointer to the descriptor to be freed, which can be yacVector or yacLobLocator. |
| type (IN)        | The type of the descriptor to be freed, optional values are YAC_DESC_VECTOR and YAC_DESC_LOB. |
