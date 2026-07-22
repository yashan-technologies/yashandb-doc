## Functionality Introduction

The yacFreeHandle function is used to free handle space. It returns YAC_SUCCESS to indicate successful release, and YAC_ERROR to indicate failure to release.

## Function Declaration

```c
yacResult yacFreeHandle ( yacHandleType type,
                          yacHandle     handle);
```

## Parameter Description

|Parameter Name |Description |
| ----------- | ---------- |
| type (IN)     | Handle type.  |
| handle (IN)   | Handle.       |