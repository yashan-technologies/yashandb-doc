## 功能简介

yacShortTimeSetShortTime函数用于设置YacShortTime的值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacShortTimeSetShortTime(YacShortTime* time,
                                   YacUint8 hour,
                                   YacUint8 minute,
                                   YacUint8 second,
                                   YacUint32 fraction);
```

## 参数说明

|  参数名| 说明|
| ------------ | ------------------------ |
| time (OUT)   | YacShortTime类型时间戳。 |
| hour (IN)    | 小时值。                 |
| minute (IN)  | 分钟值。                 |
| second(IN)   | 秒值。                   |
| fraction(IN) | 微秒值。                 |
