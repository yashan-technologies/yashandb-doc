## Functionality Introduction

The yacDateSetDate function is used to set the value of YacDate. It returns YAC_SUCCESS to indicate success, and YAC_ERROR to indicate failure.

## Function Declaration

```c
YacResult yacDateSetDate(YacDate* date,
                         YacInt16 year,
                         YacUint8 month,
                         YacUint8 day);
```

## Parameter Description

|Parameter Name |Description |
| ---------- | ------------------- |
| date(OUT)     | YacDate type SCN.              |
| year (IN)     | Year information.               |
| month (IN)    | Month information.              |
| day (IN)      | Day information.                |