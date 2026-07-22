## 功能简介

yacYMIntervalSetYearMonth函数用于设置YacYMInterval的值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacYMIntervalSetYearMonth(YacYMInterval* ymInterval,
                                    YacInt32 year,
                                    YacInt32 month);
```

## 参数说明

|  参数名| 说明|
| ---------------- | ------------------------- |
| ymInterval (OUT) | YacYMInterval类型时间差。 |
| year(IN)         | 年份信息。                      |
| month (IN)       | 月份信息。                      |
