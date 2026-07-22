## Functionality Introduction

The yacYMIntervalSetYearMonth function is used to set the value of a YacYMInterval. It returns YAC_SUCCESS to indicate success and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacYMIntervalSetYearMonth(YacYMInterval* ymInterval,
                                    YacInt32 year,
                                    YacInt32 month);
```

## Parameter Description

|Parameter Name |Description |
| ---------------- | ------------------------- |
| ymInterval (OUT)  | The YacYMInterval type duration.  |
| year (IN)        | Year information.                  |
| month (IN)       | Month information.                 |