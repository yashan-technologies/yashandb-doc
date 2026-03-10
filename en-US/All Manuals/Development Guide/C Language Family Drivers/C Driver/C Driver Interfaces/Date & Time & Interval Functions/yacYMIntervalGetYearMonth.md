## Functionality Introduction

The yacYMIntervalGetYearMonth function is used to retrieve the year and month information stored in YacYMInterval. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacYMIntervalGetYearMonth(const YacYMInterval ymInterval,
                                    YacInt32* year,
                                    YacInt32* month);
```

## Parameter Description

|Parameter Name |Description |
| --------------- | ------------------------- |
| ymInterval (IN)   | The time interval of type YacYMInterval. |
| year (OUT)        | Year information.               |
| month (OUT)       | Month information.              |