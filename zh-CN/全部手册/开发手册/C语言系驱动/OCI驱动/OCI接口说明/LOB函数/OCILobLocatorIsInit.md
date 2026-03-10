## 功能简介

OCILobLocatorIsInit函数用于判断LOB是否初始化。

## 函数声明

```c
sword OCILobLocatorIsInit ( OCIEnv               *envhp,
                            OCIError             *errhp,
                            const OCILobLocator  *locp,
                            boolean              *is_initialized);
```

## 参数说明

|  参数名| 说明|
| ------ |--------------------------------------------|
| svchp (IN/OUT) | 服务上下文句柄。               |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
| locp (IN)      | 唯一引用LOB的LOB定位器。        |
| is_initialized (OUT) | 是否初始化结果。                |
