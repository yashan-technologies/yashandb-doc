## 功能简介

yacShortTimeGetShortTime函数用于获取存储于YacShortTime的时、分和秒和微秒信息，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacShortTimeGetShortTime(const YacShortTime time,
                                   YacUint8* hour,
                                   YacUint8* minute,
                                   YacUint8* second,
                                   YacUint32* fraction);
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------ |
| time (IN)      | YacShortTime类型时间戳。 |
| hour (OUT)     | 小时值。                   |
| minute (OUT)   | 分钟值。                   |
| second (OUT)   | 秒值。                     |
| fraction (OUT) | 微秒值。                 |
