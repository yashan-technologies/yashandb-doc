## 功能简介

yacTimestampGetTimestamp函数用于获取存储于YacTimestamp的年、月、日、时、分、秒和微秒信息，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

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

## 参数说明

| 参数名         | 说明                     |
| -------------- | ------------------------ |
| timestamp(IN)  | YacTimestamp类型时间戳。 |
| year (OUT)     | 年份信息。                     |
| month (OUT)    | 月份信息。                     |
| day (OUT)      | 日信息。                     |
| hour (OUT)     | 小时值。                 |
| minute (OUT)   | 分钟值。                 |
| second (OUT)   | 秒值。                   |
| fraction (OUT) | 微秒值。                 |