## 功能简介

yacLobGetChunkSize函数用于LOB协议中获取LOB的chunkSize，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
yacResult yacLobGetChunkSize ( yacHandle      hConn,
                               yacLobLocator* locator,
                               yacUint16*     chunkSize);
```

## 参数说明

| 参数名          | 说明                                       |
| --------------- | ------------------------------------------ |
| hConn (IN/OUT)  | 记录与连接相关的多种信息的句柄。           |
| locator (IN)    | LOB协议中的loblocator。                    |
| chunkSize (OUT) | 返回获取到的chunkSize，chunkSize为字节数。 |