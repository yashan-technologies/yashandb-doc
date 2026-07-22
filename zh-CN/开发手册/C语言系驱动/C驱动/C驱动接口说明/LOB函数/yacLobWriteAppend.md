## 功能简介

yacLobWriteAppend函数用于在LOB协议中将数据追加到LOB末尾，返回YAC_SUCCESS表示追加成功，返回YAC_ERROR表示追加失败。

## 函数声明

```c
yacResult yacLobWriteAppend ( yacHandle      hConn,
                              yacLobLocator* locator,
                              YacUint64*     byteSize,
                              YacUint64*     charSize,
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
| buf (IN)          | 用户需要上传的数据。                                         |
| bufLen (IN)       | 用户上传数据的存储区长度。                                   |
