## 功能简介

yacLobDescAlloc2函数用于在LOB协议中为loblocator分配空间，返回YAC_SUCCESS表示空间分配成功，返回YAC_ERROR表示空间分配失败。

## 函数声明

```c
YacResult yacLobDescAlloc2(YacHandle hConn,
                           YacLobLocator** desc);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。 |
| desc (OUT)     | 指向被分配空间的loblocator。     |
