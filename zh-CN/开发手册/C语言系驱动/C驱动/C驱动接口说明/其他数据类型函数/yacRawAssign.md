## 功能简介

将一个yacRaw拷贝给另一个yacRaw。返回YAC_SUCCESS表示拷贝成功，返回YAC_ERROR表示拷贝失败。

## 函数声明

```c
YacResult yacRawAssign(YacHandle hEnv, const YacRaw* yacSrcRaw, YacRaw** yacDstRaw);
```

## 参数说明

|  参数名| 说明|
| --------------- | ---------------------- |
| hEnv (IN)       | 环境句柄。             |
| yacSrcRaw (IN)  | 被复制的yacRaw句柄。   |
| yacDstRaw (OUT) | 要复制到的yacRaw句柄。 |
