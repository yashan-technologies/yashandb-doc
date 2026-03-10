## 功能简介

yacLobDescFree2函数用于在LOB协议中为loblocator释放空间，返回YAC_SUCCESS表示空间释放成功，返回YAC_ERROR表示空间释放失败。

## 函数声明

```c
YacResult yacLobDescFree2(YacLobLocator* desc);
```

## 参数说明

| 参数名    | 说明             |
| --------- | ---------------- |
| desc (IN) | 指向loblocator。 |