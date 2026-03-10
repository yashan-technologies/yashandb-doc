## 功能简介

yacDSIntervalSetDaySecond函数用于设置YacDSInterval的值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacDSIntervalSetDaySecond(YacDSInterval* dsInterval,
                                    YacInt32 day,
                                    YacInt32 hour,
                                    YacInt32 minute,
                                    YacInt32 second,
                                    YacInt32 fraction);
```

## 参数说明

| 参数名           | 说明                      |
| ---------------- | ------------------------- |
| dsInterval (OUT) | YacDSInterval类型时间差。 |
| day (IN)         | 日信息。                      |
| hour (IN)        | 小时值。                  |
| minute (IN)      | 分钟值。                  |
| second (IN)      | 秒值。                    |
| fraction (IN)    | 微秒值。                  |