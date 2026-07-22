## 功能简介

yacLobWrite2函数用于在LOB协议中从某个位置将数据插入到LOB，返回YAC_SUCCESS表示写入成功，返回YAC_ERROR表示写入失败。

## 函数声明

```c
yacResult yacLobWrite2 ( yacHandle      hConn,
                         yacLobLocator* locator,
                         YacUint64*     byteSize, 
                         YacUint64*     charSize,
                         YacUint64      offset,
                         YacUint8*      buf,
                         YacUint64      bufLen);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)    | 记录与连接相关的多种信息的句柄。                             |
| locator (IN)      | LOB协议中的loblocator。                                      |
| byteSize (IN/OUT) | 要写入数据的字节长度。<br>对于CLOB在charSize不为零时自动忽略，返回写入数据的实际字节长度。 |
| charSize (IN/OUT) | 要写入数据的字符长度。<br>对于BLOB自动忽略，返回写入数据的实际字符长度。 |
| offset (IN)       | 需要插入LOB的偏移位置。<br>如果loblocator指向CLOB或NCLOB，offset为字符表示。<br>如果loblocator指向bLOB，offset为字节表示。 |
| buf (IN)          | 用户需要上传的数据。                                         |
| bufLen(IN)        | 用户上传数据的存储区长度。                                   |
