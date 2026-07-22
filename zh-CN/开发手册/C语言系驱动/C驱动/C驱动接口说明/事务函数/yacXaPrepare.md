## 功能简介

yacXaPrepare函数用于对XA事务进行预提交，返回YAC_SUCCESS表示预提交成功，返回YAC_ERROR表示预提交失败。

## 函数声明

```c
yacResult yacXaPrepare(yacHandle hConn,
                       YacXid* xid,
                       YacUint32 flags);
```

## 参数说明

|  参数名     | 说明           |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |
| xid (IN)   | XA事务全局唯一标识。 |
| flags (IN) | 保留参数，请使用YAC_TMNOFLAGS (0)。 |
