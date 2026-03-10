## 功能简介

yacLobGetLength函数用于LOB协议中获取LOB数据总长度，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
yacResult yacLobGetLength ( yacHandle      hConn,
                            yacLobLocator* locator,
                            yacUint64*     length);
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。                             |
| locator (IN)   | LOB协议中的loblocator。                                      |
| length (OUT)   | 存储获取到的数据总长度。<br>如果loblocator指向BLOB，length返回字节长度。<br>如果loblocator指向CLOB或NCLOB，length返回字符长度。 |