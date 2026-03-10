## Functionality Overview

The yacShortTimeGetShortTime function is used to retrieve the hour, minute, second, and microsecond information stored in YacShortTime. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacShortTimeGetShortTime(const YacShortTime time,
                                   YacUint8* hour,
                                   YacUint8* minute,
                                   YacUint8* second,
                                   YacUint32* fraction);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------ |
| time (IN)       | YacShortTime type SCN.      |
| hour (OUT)      | Hour value.                 |
| minute (OUT)    | Minute value.               |
| second (OUT)    | Second value.               |
| fraction (OUT)  | Microsecond value.          |