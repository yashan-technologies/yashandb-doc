## Functionality Introduction

The `yacTimestampGetTimestamp` function is used to retrieve the year, month, day, hour, minute, second, and microsecond information stored in a YacTimestamp. It returns `YAC_SUCCESS` to indicate success and `YAC_ERROR` to indicate failure.

## Function Declaration

```c
YacResult yacTimestampGetTimestamp(const YacTimestamp timestamp,
                                   YacInt16* year,
                                   YacUint8* month,
                                   YacUint8* day,
                                   YacUint8* hour,
                                   YacUint8* minute,
                                   YacUint8* second,
                                   YacUint32* fraction);
```

## Parameter Description

|Parameter Name |Description |
| -------------- | ------------------------ |
| timestamp (IN)   | YacTimestamp type SCN.          |
| year (OUT)       | Year information.                |
| month (OUT)      | Month information.               |
| day (OUT)        | Day information.                 |
| hour (OUT)       | Hour value.                     |
| minute (OUT)     | Minute value.                   |
| second (OUT)     | Second value.                   |
| fraction (OUT)   | Microsecond value.              |