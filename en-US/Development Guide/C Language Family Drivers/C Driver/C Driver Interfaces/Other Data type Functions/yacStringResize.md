## Functionality Overview

Adjusts the memory size of the given yacString. Returns YAC_SUCCESS if the adjustment is successful, or YAC_ERROR if it fails.

## Function Declaration

```c
YacResult yacStringResize(YacHandle hEnv, YacUint32 newSize, YacString** yacString);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------------ |
| hEnv (IN)             | Environment handle.                  |
| newSize (IN)          | The memory size to adjust to (in bytes). |
| yacString (IN/OUT)    | yacString handle.                    |

To initialize a yacString, you must first call yacStringAssignText or yacStringResize.

To free memory, you must call yacStringResize with newSize set to 0.