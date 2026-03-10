## 功能简介

OCITransForget函数用于从挂起事务表中删除事务的状态。

## 函数声明

```c
sword OCITransForget ( OCISvcCtx     *svchp, 
                       OCIError      *errhp,
                       ub4           flags);
```

## 参数说明

| 参数名         | 说明                     |
|-------------|------------------------|
| svchp (IN)  | 服务上下文句柄。               |
| errhp (IN)  | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| flags (IN)  | 用于全局事务的标志。             |

部分参数目前仅支持以下取值：

| 参数     | 有效值        |
|--------|------------|
| flags  | TMNOFLAGS  |

