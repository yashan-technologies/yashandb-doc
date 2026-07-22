## Functionality Introduction

The yacShortTimeSetShortTime function is used to set the value of YacShortTime. It returns YAC_SUCCESS to indicate a successful setting, and returns YAC_ERROR to indicate a failure in setting.

## Function Declaration

```c
YacResult yacShortTimeSetShortTime(YacShortTime* time,
                                   YacUint8 hour,
                                   YacUint8 minute,
                                   YacUint8 second,
                                   YacUint32 fraction);
```

## Parameter Description

|Parameter Name |Description |
| ------------ | ------------------------ |
| time (OUT)     | YacShortTime type SCN.         |
| hour (IN)      | Hour value.                    |
| minute (IN)    | Minute value.                  |
| second (IN)    | Second value.                  |
| fraction (IN)  | Microsecond value.             |