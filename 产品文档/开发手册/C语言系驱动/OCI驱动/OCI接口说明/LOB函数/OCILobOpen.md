## 功能简介

OCILobOpen函数用于在指定的模式下打开LOB。

## 函数声明

```c
sword OCILobOpen ( OCISvcCtx        *svchp,
                   OCIError         *errhp, 
                   OCILobLocator    *locp, 
                   ub1               mode );
```

## 参数说明

| 参数名         | 说明                                         |
| -------------- | -------------------------------------------- |
| svchp (IN)     | 服务上下文句柄。                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| locp (IN)      | 要打开的LOB。定位器可以引用内部或外部LOB。   |
| mode (IN)      | 打开LOB的模式。                              |


