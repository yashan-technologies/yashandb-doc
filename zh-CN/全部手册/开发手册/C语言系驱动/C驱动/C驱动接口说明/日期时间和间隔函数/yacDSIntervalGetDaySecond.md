## 功能简介

yacDSIntervalGetDaySecond函数用于获取存储于YacDSInterval的日、时、分、秒和微秒信息，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacDSIntervalGetDaySecond(const YacDSInterval dsInterval,
                                    YacInt32* day,
                                    YacInt32* hour,
                                    YacInt32* minute,
                                    YacInt32* second,
                                    YacInt32* fraction);
```

## 参数说明

|  参数名| 说明|
| --------------- | ------------------------- |
| dsInterval (IN) | YacDSInterval类型时间差。 |
| day (OUT)       | 日信息。                      |
| hour (OUT)      | 小时值。                  |
| minute (OUT)    | 分钟值。                  |
| second (OUT)    | 秒值。                    |
| fraction (OUT)  | 微秒值。                  |
