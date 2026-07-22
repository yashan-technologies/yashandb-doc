## 功能简介

yacXaForget函数用于丢弃与XA事务相关的recovery信息，返回YAC_SUCCESS表示成功，返回YAC_ERROR表示失败。

## 函数声明

```c
yacResult yacXaForget(yacHandle hConn,
                     YacXid* xid,
                     YacUint32 flags);
```

## 参数说明

|  参数名     | 说明           |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |
| xid (IN)   | XA事务全局唯一标识。 |
| flags (IN) | 事务标志位：<br />- YAC_TMNOFLAGS (0)：正常丢弃recovery信息<br />- YAC_TMJOIN (0x00200000)：丢弃已加入事务分支的recovery信息 |

> **Note**:
> 
> 如果事务已经执行了Rollback操作，则不能再调用Forget，否则会返回YAC_ERROR。