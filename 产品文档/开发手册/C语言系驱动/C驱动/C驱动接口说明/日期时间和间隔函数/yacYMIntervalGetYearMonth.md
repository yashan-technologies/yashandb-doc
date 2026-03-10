## 功能简介

yacYMIntervalGetYearMonth函数用于获取存储于YacYMInterval的年和月信息，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacYMIntervalGetYearMonth(const YacYMInterval ymInterval,
                                    YacInt32* year,
                                    YacInt32* month);
```

## 参数说明

| 参数名          | 说明                      |
| --------------- | ------------------------- |
| ymInterval (IN) | YacYMInterval类型时间差。 |
| year (OUT)      | 年份信息。                      |
| month (OUT)     | 月份信息。                      |

