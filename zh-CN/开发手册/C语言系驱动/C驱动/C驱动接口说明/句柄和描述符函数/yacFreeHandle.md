## 功能简介

yacFreeHandle函数用于释放句柄空间，返回YAC_SUCCESS表示释放成功，返回YAC_ERROR表示释放失败。

## 函数声明

```c
yacResult yacFreeHandle ( yacHandleType type,
                          yacHandle     handle);
```

## 参数说明

|  参数名| 说明|
| ----------- | ---------- |
| type (IN)   | 句柄类型。 |
| handle (IN) | 句柄。     |
