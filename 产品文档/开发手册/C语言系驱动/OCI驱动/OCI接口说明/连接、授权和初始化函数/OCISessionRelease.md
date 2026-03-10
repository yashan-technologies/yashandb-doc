## 功能简介

OCISessionRelease函数用于释放用户会话。

## 函数声明

```c
sword OCISessionRelease ( OCISvcCtx       *svchp,
                          OCIError        *errhp,
                          OraText         *tag,
                          ub4              tag_len,
                          ub4              mode );
```

## 参数说明

| 参数名        | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| svchp(IN)     | 服务上下文的句柄。                                           |
| errhp(IN/OUT) | 一个错误句柄。<br />当出现错误时，可以将其传递给[OCIErrorGet](../辅助功能函数/OCIErrorGet)以获取诊断信息。 |
| tag (IN)      | 释放标识，保留参数。                                         |
| tag_len(IN)   | 释放标识长度，保留参数。                                     |
| mode(IN)      | 释放模式，保留参数。                                         |

