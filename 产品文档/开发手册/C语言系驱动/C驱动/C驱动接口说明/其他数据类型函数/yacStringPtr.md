## 功能简介

获取yacString中存放字符串。返回值表示存放字符串的指针。

## 函数声明

```c
YacChar*  yacStringPtr(YacHandle hEnv, const YacString* yacString);
```

## 参数说明

| 参数名            | 说明            |
| ----------------- | --------------- |
| hEnv (IN)         | 环境句柄。      |
| yacString (IN)    | yacString句柄。 |