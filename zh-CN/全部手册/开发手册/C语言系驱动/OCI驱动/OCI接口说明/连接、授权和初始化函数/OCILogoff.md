## 功能简介

OCILogoff函数用于退出登录。

## 函数声明

```c
sword   OCILogoff (OCISvcCtx *svchp, OCIError *errhp);
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------------------------ |
| svchp (IN)     | 服务上下文句柄。                             |
| errhp (IN/OUT) | 一个错误句柄，当出现错误时可以获取诊断信息。 |
