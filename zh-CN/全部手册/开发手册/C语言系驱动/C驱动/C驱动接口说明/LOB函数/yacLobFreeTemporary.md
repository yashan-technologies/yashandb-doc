## 功能简介

yacLobFreeTemporary函数用于在LOB协议中通知服务端释放临时LOB，返回YAC_SUCCESS表示释放成功，返回YAC_ERROR表示释放失败。

## 函数声明

```c
yacResult yacLobFreeTemporary ( yacHandle      hConn,
                                yacLobLocator* locator);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。 |
| locator (IN)   | LOB协议中的loblocator。          |
