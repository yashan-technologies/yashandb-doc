## Functionality Overview

The `yacDSIntervalGetDaySecond` function is used to retrieve the day, hour, minute, second, and microsecond information stored in a `YacDSInterval`. It returns `YAC_SUCCESS` to indicate a successful retrieval, and `YAC_ERROR` to indicate a failure.

## Function Declaration

```c
YacResult yacDSIntervalGetDaySecond(const YacDSInterval dsInterval,
                                    YacInt32* day,
                                    YacInt32* hour,
                                    YacInt32* minute,
                                    YacInt32* second,
                                    YacInt32* fraction);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------- |
| dsInterval (IN)    | Time interval of type `YacDSInterval`. |
| day (OUT)          | Day information.            |
| hour (OUT)         | Hour value.                 |
| minute (OUT)       | Minute value.               |
| second (OUT)       | Second value.               |
| fraction (OUT)     | Microsecond value.          |