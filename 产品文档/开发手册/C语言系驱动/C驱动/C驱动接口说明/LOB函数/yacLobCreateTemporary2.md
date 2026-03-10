## 功能简介

yacLobCreateTemporary2函数用于在LOB协议中通知服务端创建临时LOB，返回YAC_SUCCESS表示创建成功，返回YAC_ERROR表示创建失败。

## 函数声明

```c
YacResult yacLobCreateTemporary2(YacHandle hConn,
                                 YacLobLocator* loc,
                                 YacTempLobType tempLobType)
```

## 参数说明

| 参数名           | 说明                             |
| ---------------- | -------------------------------- |
| hConn (IN/OUT)   | 记录与连接相关的多种信息的句柄。 |
| loc (IN)         | LOB协议中的loblocator。          |
| tempLobType (IN) | 需要创建的临时LOB的类型。            |