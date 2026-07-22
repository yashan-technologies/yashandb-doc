## 功能简介

yacXaRollback函数用于回滚XA事务，返回YAC_SUCCESS表示回滚成功，返回YAC_ERROR表示回滚失败。

## 函数声明

```c
yacResult yacXaRollback(yacHandle hConn,
                       YacXid* xid,
                       YacUint32 flags);
```

## 参数说明

|  参数名     | 说明            |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |
| xid (IN)   | XA事务全局唯一标识。 |
| flags (IN) | 事务标志位：<br />- YAC_TMNOFLAGS (0)：正常回滚事务分支<br />- YAC_TMJOIN (0x00200000)：回滚已加入的事务分支 |