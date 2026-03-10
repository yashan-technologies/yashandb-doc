## 功能简介

将一个yacString拷贝给另一个yacString。返回YAC_SUCCESS表示拷贝成功，返回YAC_ERROR表示拷贝失败。

## 函数声明

```c
YacResult yacStringAssign(YacHandle hEnv, const YacString* yacSrcString, YacString** yacDstString);
```

## 参数说明

| 参数名             | 说明                      |
| ------------------ | ------------------------- |
| hEnv (IN)          | 环境句柄。                |
| yacSrcString (IN)  | 被复制的yacString句柄。   |
| yacDstString (OUT) | 要复制到的yacString句柄。 |