## 功能简介
yacXaStart函数用于启动一个XA事务，返回YAC_SUCCESS表示启动成功，返回YAC_ERROR表示启动失败。


## 函数声明

```c
yacResult yacXaStart(yacHandle hConn,
                    YacXid* xid,
                    YacUint32 flags,
                    YacUint32 timeout);
```

## 参数说明
|  参数名     | 说明          |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |
| xid (IN)   | XA事务全局唯一标识，结构体包含formatID、gtrid和bqual字段。 |
| flags (IN) | 事务标志位：<br />- YAC_TMNOFLAGS (0)：启动新的事务分支<br />- YAC_TMJOIN (0x00200000)：加入已存在的事务分支 |
| timeout (IN) | 事务超时时间（单位：秒），0表示使用系统默认超时。 |