## 功能简介

OCIDateTimeConstruct函数用于构造日期时间描述符。

## 函数声明

```c
sword OCIDateTimeConstruct ( void          *hndl,
                             OCIError      *err,
                             OCIDateTime   *datetime,
                             sb2            year,
                             ub1            month,
                             ub1            day,
                             ub1            hour,
                             ub1            min,
                             ub1            sec,
                             ub4            fsec,
                             OraText       *timezone,
                             size_t         timezone_length );
```

## 参数说明

| 参数名               | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| hndl (IN)            | 用户会话句柄或环境句柄。                                     |
| err (IN/OUT)         | OCI错误句柄。<br />如果出现错误，则记录在err中，此函数返回OCI_error。<br />通过调用[OCIErrorGet](../辅助功能函数/OCIErrorGet)获取诊断信息。 |
| datetime (IN)        | 指向OCIDateTime描述符的指针。                                |
| year (IN)            | 年份值。                                                     |
| month (IN)           | 月份值。                                                     |
| day (IN)             | 日值。                                                       |
| hour (IN)            | 小时值。                                                     |
| min (IN)             | 分钟值。                                                     |
| sec (IN)             | 秒值。                                                       |
| fsec (IN)            | 小数秒值。                                                   |
| timezone (IN)        | 时区字符串。                                                 |
| timezone_length (IN) | 时区字符串的长度。                                           |

部分参数目前仅支持以下取值：

| 参数            | 有效值 |
| --------------- | ------ |
| timezone        | NULL   |
| timezone_length | 0      |
