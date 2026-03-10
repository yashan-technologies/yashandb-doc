## 功能简介

yacLobRead2WithCharset函数用于在LOB协议指定从某个位置使用特定字符集读取LOB数据，返回YAC_SUCCESS表示读取成功，返回YAC_ERROR表示读取失败。

## 函数声明

```c
yacResult yacLobRead2WithCharset ( yacHandle       hConn,
                                   yacLobLocator*  locator,
                                   YacUint64*      byteSize,
                                   YacUint64*      charSize,
                                   YacUint64       offset,
                                   YacUint8*       buf,
                                   YacUint64       bufLen,
                                   YacCharsetCode  charset);
```

## 参数说明

|  参数名| 说明|
| ----------------- | ----------------------------------------------------------- |
| hConn (IN/OUT)    | 记录与连接相关的多种信息的句柄。                             |
| locator (IN)      | LOB协议中的loblocator。                                   |
| byteSize (IN/OUT) | 要写入数据的字节长度。<br>对于CLOB在charSize不为零时自动忽略，返回写入数据的实际字节长度。 |
| charSize (IN/OUT) | 要写入数据的字符长度。<br>对于BLOB自动忽略，返回写入数据的实际字符长度。 |
| offset (IN)       | 需要插入LOB的偏移位置。<br>如果loblocator指向CLOB或NCLOB，offset为字符表示。<br>如果loblocator指向BLOB，offset为字节表示。 |
| buf (OUT)         | 存储要读取到的缓冲区。                                 |
| bufLen(OUT)       | 存储要读取到的缓冲区长度。                                   |
| charset(IN)       | 读取数据使用的字符集，可选项为[YAC_CHARSET_ASCII,YAC_CHARSET_GBK,YAC_CHARSET_UTF8,YAC_CHARSET_ISO88591,YAC_CHARSET_UTF16,YAC_CHARSET_GB18030]。                                   |
