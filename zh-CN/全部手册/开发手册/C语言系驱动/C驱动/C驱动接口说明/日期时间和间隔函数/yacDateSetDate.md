## 功能简介

yacDateSetDate函数用于设置YacDate的值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacDateSetDate(YacDate* date,
                         YacInt16 year,
                         YacUint8 month,
                         YacUint8 day);
```

## 参数说明

|  参数名| 说明|
| ---------- | ------------------- |
| date(OUT)  | YacDate类型时间戳。 |
| year (IN)  | 年份信息。                |
| month (IN) | 月份信息。                |
| day (IN)   | 日信息。                |
