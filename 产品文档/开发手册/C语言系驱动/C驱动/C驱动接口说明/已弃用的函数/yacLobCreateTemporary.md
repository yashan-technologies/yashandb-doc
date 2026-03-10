## 功能简介

yacLobCreateTemporary函数用于LOB协议中通知服务端创建临时LOB，返回YAC_SUCCESS表示创建成功，返回YAC_ERROR表示创建失败。

> **Note**：
>
> 该函数已弃用，推荐使用[yacLobCreateTemporary2](../LOB函数/yacLobCreateTemporary2)。

## 函数声明

```c
yacResult yacLobCreateTemporary ( yacHandle      hConn,
                                  yacLobLocator* locator);
```

## 参数说明

| 参数名         | 说明                             |
| -------------- | -------------------------------- |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。 |
| locator (IN)   | LOB协议中的loblocator。          |