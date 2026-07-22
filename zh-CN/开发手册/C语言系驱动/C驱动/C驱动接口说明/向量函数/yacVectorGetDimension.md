## 功能简介

yacVectorGetDimension函数用于获取yacVector对象中向量的维度。返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacVectorGetDimension(YacVector* vector,
                                YacUint16* dim);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| vector (IN/OUT) | 用于获取维度的源vector描述符。 |
| dim (OUT)       | 向量的维度。 |
