## Functionality Introduction

The yacDSIntervalSetDaySecond function is used to set the value of YacDSInterval. A return value of YAC_SUCCESS indicates successful setting, while a return value of YAC_ERROR indicates failure to set.

## Function Declaration

```c
YacResult yacDSIntervalSetDaySecond(YacDSInterval* dsInterval,
                                    YacInt32 day,
                                    YacInt32 hour,
                                    YacInt32 minute,
                                    YacInt32 second,
                                    YacInt32 fraction);
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------- |
| dsInterval (OUT)  | Time interval of YacDSInterval type. |
| day (IN)          | Day information.              |
| hour (IN)         | Hour value.                   |
| minute (IN)       | Minute value.                 |
| second (IN)       | Second value.                 |
| fraction (IN)     | Microsecond value.            |