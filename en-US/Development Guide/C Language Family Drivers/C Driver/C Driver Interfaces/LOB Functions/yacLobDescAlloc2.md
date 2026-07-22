## Functionality Introduction

The yacLobDescAlloc2 function is used to allocate space for a loblocator in the LOB protocol. Returning YAC_SUCCESS indicates that the space allocation was successful, while returning YAC_ERROR indicates that the space allocation failed.

## Function Declaration

```c
YacResult yacLobDescAlloc2(YacHandle hConn,
                           YacLobLocator** desc);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | -------------------------------- |
| hConn (IN/OUT)   | A handle that records various information related to the connection. |
| desc (OUT)       | A pointer to the loblocator that has been allocated space. |