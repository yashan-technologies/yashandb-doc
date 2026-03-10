## 功能简介

yacLobTrim函数用于在LOB协议中截取LOB数据，返回YAC_SUCCESS表示截取成功，返回YAC_ERROR表示截取失败。

## 函数声明

```c
yacResult yacLobTrim ( yacHandle      hConn,
                       yacLobLocator* locator,
                       YacUint64*     newlen);
```

## 参数说明

| 参数名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。                             |
| locator (IN)   | LOB协议中的loblocator。                                      |
| newlen(IN)     | LOB要截取的新长度。<br>若loblocator指向CLOB或NCLOB，则newlen为字符表示。<br>若loblocator指向BLOB，则newlen为字节表示。 |