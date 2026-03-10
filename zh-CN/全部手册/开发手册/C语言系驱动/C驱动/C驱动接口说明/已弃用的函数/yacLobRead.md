## 功能简介

yacLobRead函数用于LOB协议中读取服务端LOB中的数据并存储到buf，返回YAC_SUCCESS表示读取成功，返回YAC_ERROR表示读取失败。

> **Note**: 
>
> 该函数已弃用，推荐使用[yacLobRead2](../LOB函数/yacLobRead2)。

## 函数声明

```c
yacResult yacLobRead ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     bytes,
                       YacUint8*      buf,
                       YacUint64      bufLen);
```

## 参数说明

|  参数名| 说明|
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。                             |
| locator (IN)   | LOB协议中的loblocator。                                      |
| bytes (IN/OUT) | 输入：需要写入的数据总长度。<br/>输出：实际写入的数据总长度。<br/>均用字节数表示。 |
| buf (OUT)      | 存储读取到的数据。                                           |
| bufLen (IN)    | 预留参数，暂未在该接口中使用。                               |
