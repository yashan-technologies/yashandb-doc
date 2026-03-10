## 功能简介

yacTimestampSetTimestamp函数用于设置YacTimestamp的值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

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

## 参数说明

|  参数名| 说明|
| --------------- | ------------------------ |
| timestamp (OUT) | YacTimestamp类型时间戳。 |
| year (IN)       | 年份信息。                     |
| month (IN)      | 月份信息。                     |
| day (IN)        | 日信息。                     |
| hour (IN)       | 小时值。                 |
| minute (IN)     | 分钟值。                 |
| second (IN)      | 秒值。                   |
| fraction (IN)   | 微秒值。                 |
