## 功能简介

yacLobIsTemporary函数用于在LOB协议中判断LOB是否为临时LOB，返回YAC_SUCCESS表示操作成功，返回YAC_ERROR表示操作失败。

> **Note**:
> 
> 建议在fetch LOB操作后，调用yacLobIsTemporary函数判断loblocator是否指向临时LOB。若是，则调用[yacLobFreeTemporary](yacLobFreeTemporary)释放该临时LOB，以免服务端临时LOB累积，从而显著降低系统速度。

## 函数声明

```c
yacResult yacLobIsTemporary ( yacHandle      hConn,
                              yacLobLocator* locator,
                              YacBool*       isTemporary);
```

## 参数说明

|  参数名| 说明|
| ------------------- | --------------------------------------------- |
| hConn (IN/OUT)      | 记录与连接相关的多种信息的句柄。              |
| locator (IN)        | LOB协议中的loblocator。                       |
| isTemporary(IN/OUT) | YAC_TRUE表示临时LOB，YAC_FALSE表示不是临时LOB。 |
