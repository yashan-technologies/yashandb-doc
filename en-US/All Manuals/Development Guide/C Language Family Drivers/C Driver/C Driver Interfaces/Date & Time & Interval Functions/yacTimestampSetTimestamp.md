## Functionality Introduction

The yacTimestampSetTimestamp function is used to set the value of YacTimestamp. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacTimestampSetTimestamp(YacTimestamp* timestamp,
                                   YacInt16 year,
                                   YacUint8 month,
                                   YacUint8 day,
                                   YacUint8 hour, 
                                   YacUint8 minute,
                                   YacUint8 second,
                                   YacUint32 fraction);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------ |
| timestamp (OUT)   | YacTimestamp type SCN.         |
| year (IN)         | Year information.              |
| month (IN)        | Month information.             |
| day (IN)          | Day information.               |
| hour (IN)         | Hour value.                    |
| minute (IN)       | Minute value.                  |
| second (IN)       | Second value.                  |
| fraction (IN)     | Microsecond value.             |