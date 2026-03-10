## 功能简介

OCILobClose函数用于关闭以前打开的LOB。

## 函数声明

```c
sword OCILobClose ( OCISvcCtx      *svchp,
                    OCIError       *errhp, 
                    OCILobLocator  *locp );
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------------------- |
| svchp (IN)     | 服务上下文句柄。                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| locp (IN/OUT)  | 要关闭的LOB。定位器可以引用内部或外部LOB。   |
