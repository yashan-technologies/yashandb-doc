## 功能简介

yacLobAppend函数用于在LOB协议中将一个LOB追加到另一个LOB末尾，返回YAC_SUCCESS表示追加成功，返回YAC_ERROR表示追加失败。

## 函数声明

```c
yacResult yacLobAppend ( yacHandle      hConn,
                         YacLobLocator* dstLob, 
                         YacLobLocator* srcLob);
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| hConn (IN/OUT)  | 记录与连接相关的多种信息的句柄。                             |
| dstLob (IN/OUT) | LOB协议中的loblocator，要追加数据的LOB。                     |
| srcLob (IN)     | LOB协议中的loblocator，作为追加数据的LOB，目前只支持临时LOB。<br>srcLob和dstLob的类型必须一致，即全为BLOB或全为CLOB。<br>目前只支持临时LOB。 |