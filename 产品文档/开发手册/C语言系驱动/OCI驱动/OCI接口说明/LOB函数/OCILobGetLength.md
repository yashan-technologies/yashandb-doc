## 功能简介

OCILobGetLength函数用于获取LOB的长度。

## 函数声明

```c
sword OCILobGetLength ( OCISvcCtx      *svchp,
                        OCIError       *errhp,
                        OCILobLocator  *locp,
                        ub4            *lenp );
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| svchp (IN)     | 服务上下文句柄。                                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。                 |
| locp (IN)      | 唯一引用LOB的LOB定位器。                                     |
| lenp (OUT)     | 如果LOB不为NULL，则lenp返回LOB的长度：<br/>如果指向BLOB，lenp返回字节长度。<br/>如果指向CLOB或NCLOB，lenp返回字符长度。 |

