## Functionality Introduction

The yacDateGetDate function is used to retrieve the year, month, and day information stored in YacDate. It returns YAC_SUCCESS to indicate a successful retrieval, and YAC_ERROR to indicate a failure.

## Function Declaration

```c
YacResult yacDateGetDate(const YacDate date,
                         YacInt16* year,
                         YacUint8* month,
                         YacUint8* day);
```

## Parameter Description

|Parameter Name |Description |
| ----------- | ------------------- |
| date (IN)     | YacDate type SCN.        |
| year (OUT)    | Year information.         |
| month (OUT)   | Month information.        |
| day (OUT)     | Day information.          |