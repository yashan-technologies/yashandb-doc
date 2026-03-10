## 功能简介

OCILobIsOpen函数用于判断LOB是否打开。

## 函数声明

```c
sword OCILobIsOpen ( OCISvcCtx        *svchp,
                     OCIError         *errhp, 
                     OCILobLocator    *locp, 
                     boolean          *flag );
```

## 参数说明

|  参数名| 说明|
| ------ |------------------------|
| svchp (IN/OUT) | 服务上下文句柄。               |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| locp (IN)      | 唯一引用LOB的LOB定位器。        |
| flag (OUT) | 是否打开结果。                |
