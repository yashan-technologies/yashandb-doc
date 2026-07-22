## 功能简介

yacLobWrite函数用于LOB协议中上传buf中的数据并存到服务端LOB中，返回YAC_SUCCESS表示写入成功，返回YAC_ERROR表示写入失败。

> **Note**: 
>
> 该函数已弃用，推荐使用[yacLobWrite2](../LOB函数/yacLobWrite2)。

## 函数声明

```c
yacResult yacLobWrite ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     bytes,
                       YacUint8*      buf,
                       YacUint64      bufLen);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------------- |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。       |
| locator (IN)   | LOB协议中的loblocator。                |
| bytes (IN)     | 预留参数，暂未在该接口中使用。         |
| buf (IN)       | 用户需要上传的数据。                   |
| bufLen(IN)     | 用户上传数据的实际长度，用字节数表示。 |
