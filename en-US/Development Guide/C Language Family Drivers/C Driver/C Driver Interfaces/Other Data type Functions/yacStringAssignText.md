## Functionality Overview

Assign the source text string to yacString. Returns YAC_SUCCESS to indicate successful assignment, and returns YAC_ERROR to indicate assignment failure.

## Function Declaration

```c
YacResult yacStringAssignText(YacHandle hEnv, const YacChar* str, YacUint32 strLen, YacString** yacString);
```

## Parameter Description

|Parameter Name |Description |
| --------------------- | ------------------------------ |
| hEnv (IN)             | Environment handle.             |
| str (IN)              | Source string.                  |
| strLen (IN)           | Length of the source string (in bytes). |
| yacSrcString (IN/OUT) | The yacString handle to be modified. |

To initialize yacString, you must first call yacStringAssignText or yacStringResize.