## 功能简介

OCITransDetach函数用于将事务变为非活动状态。

## 函数声明

```c
sword OCITransDetach ( OCISvcCtx    *srvcp,
                       OCIError     *errhp,
                       ub4          flags);
```

## 参数说明

| 参数名         | 说明                     |
|-------------|------------------------|
| svchp (IN)  | 服务上下文句柄。               |
| errhp (IN)  | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| flags (IN)  | 用于全局事务中的标志。            |

部分参数目前仅支持以下取值：

| 参数     | 有效值        |
|--------|------------|
| flags  | TMNOFLAGS  |

