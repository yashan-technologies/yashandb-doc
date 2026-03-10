## 功能简介

yacDateGetDate函数用于获取存储于YacDate的年、月和日信息，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacDateGetDate(const YacDate date,
                         YacInt16* year,
                         YacUint8* month,
                         YacUint8* day);
```

## 参数说明

| 参数名      | 说明                |
| ----------- | ------------------- |
| date (IN)   | YacDate类型时间戳。 |
| year (OUT)  | 年份信息。                |
| month (OUT) | 月份信息。                |
| day (OUT)   | 日信息。                |