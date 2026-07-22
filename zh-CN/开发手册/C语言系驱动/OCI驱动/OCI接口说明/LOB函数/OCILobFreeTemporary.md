## 功能简介

OCILobFreeTemporary函数用于释放临时LOB。


## 函数声明

```c
sword OCILobFreeTemporary( OCISvcCtx          *svchp,
                           OCIError           *errhp,
                           OCILobLocator      *locp);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------------------- |
| svchp (IN/OUT) | 服务上下文句柄。                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| locp (IN/OUT)  | 唯一引用LOB的内部LOB定位器。                 |
