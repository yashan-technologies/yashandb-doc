## 功能简介

OCIPing函数用于探测服务端是否异常。

## 函数声明

```c
sword OCIPing ( OCISvcCtx     *svchp,
                OCIError      *errhp,
                ub4            mode );
```

## 参数说明

|  参数名| 说明|
| --------- | -------------------------------------- |
| svchp(IN) | 初始化的服务器上下文的句柄。             |
| errhp(IN) | 错误句柄，当出现错误时可以获取诊断信息。 |
| mode(IN)  | 模式，保留参数。                     |
