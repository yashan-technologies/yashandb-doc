## 功能简介

yacLobDescFree函数用于在LOB协议中为loblocator释放空间，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

> **Note**：
>
> 该函数已弃用，推荐使用[yacLobDescFree2](../LOB函数/yacLobDescFree2)。


## 函数声明

```c
yacResult yacLobDescFree ( YacVoid* desc,
                           yacType  type);
```

## 参数说明

| 参数名    | 说明             |
| --------- | ---------------- |
| desc (IN) | 指向loblocator。 |
| type (IN) | LOB类型。        |