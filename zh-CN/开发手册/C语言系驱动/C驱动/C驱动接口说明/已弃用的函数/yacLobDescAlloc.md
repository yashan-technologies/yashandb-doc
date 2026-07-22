## 功能简介

yacLobDescAlloc函数用于在LOB协议中为loblocator分配空间，返回YAC_SUCCESS表示分配成功，返回YAC_ERROR表示分配失败。

> **Note**: 
>
> 该函数已弃用，推荐使用[yacLobDescAlloc2](../LOB函数/yacLobDescAlloc2)。

## 函数声明

```c
yacResult yacLobDescAlloc ( yacHandle   hConn,
                            yacType     type,
                            YacVoid**   desc);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| hConn (IN/OUT) | 记录与连接相关的多种信息的句柄。 |
| type (IN)      | LOB类型。                        |
| desc (OUT)     | 指向被分配空间的loblocator。     |
