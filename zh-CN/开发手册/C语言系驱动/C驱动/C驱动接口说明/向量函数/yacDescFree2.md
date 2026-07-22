## 功能简介

yacDescFree2函数用于释放描述符空间，支持向量描述符（YAC_DESC_VECTOR）和LOB描述符（YAC_DESC_LOB）的释放。返回YAC_SUCCESS表示空间释放成功，返回YAC_ERROR表示空间释放失败。

## 函数声明

```c
YacResult yacDescFree2(YacHandle hEnv,
                       YacVoid** desc,
                       YacDescType type);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| hEnv (IN/OUT) | 记录与环境相关的多种信息的句柄。 |
| desc (OUT)     | 指向被释放空间的描述符，可以是yacVector或yacLobLocator。 |
| type (IN)      | 被释放描述符的类型，可选值为YAC_DESC_VECTOR和YAC_DESC_LOB。 |
