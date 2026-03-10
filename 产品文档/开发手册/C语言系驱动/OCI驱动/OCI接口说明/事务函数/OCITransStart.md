## 功能简介

OCITransStart函数用于开启或恢复事务。

## 函数声明

```c
sword OCITransStart ( OCISvcCtx    *svchp, 
                      OCIError     *errhp, 
                      uword        timeout,
                      ub4          flags);
```

## 参数说明

| 参数名          | 说明                     |
|--------------|------------------------|
| svchp (IN)   | 服务上下文句柄。               |
| errhp (IN)   | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| timeout (IN) | 超时时间（单位：秒）。            |
| flags (IN)   | 指定事务操作是启动新事务还是恢复已有事务。  |

部分参数目前仅支持以下取值：

| 参数     | 有效值                                |
|--------|------------------------------------|
| flags  | TMNOFLAGS<br/>TMJOIN<br/>TMRESUME  |

