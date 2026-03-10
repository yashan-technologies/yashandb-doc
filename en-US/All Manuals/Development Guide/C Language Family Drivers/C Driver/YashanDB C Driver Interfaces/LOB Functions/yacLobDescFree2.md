## Functionality Introduction

The `yacLobDescFree2` function is used to release space for a loblocator in the LOB protocol. It returns `YAC_SUCCESS` to indicate that the space has been successfully released, and returns `YAC_ERROR` to indicate that the space release has failed.

## Function Declaration

```c
YacResult yacLobDescFree2(YacLobLocator* desc);
```

## Parameter Explanation

|Parameter Name |Description |
| --------- | ---------------- |
| desc (IN)     | A pointer to the loblocator. |