## 功能简介

yacCancel函数用于客户端发起异步取消服务端当前处理逻辑的请求，返回YAC_SUCCESS表示取消成功，返回YAC_ERROR表示取消失败。

## 函数声明

```c
YacResult yacCancel(YacHandle hConn);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------- |
| hConn (IN/OUT) | 连接信息句柄。 |
