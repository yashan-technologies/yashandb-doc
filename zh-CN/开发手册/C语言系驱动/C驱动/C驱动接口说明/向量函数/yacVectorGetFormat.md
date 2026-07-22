## 功能简介

yacVectorGetFormat函数用于获取yacVector对象中向量的数值类型。返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacVectorGetFormat(YacVector* vector,
                             YacVectorFormat* format);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| vector (IN/OUT) | 用于获取数值类型的源vector描述符。 |
| format (OUT)    | 向量的数值类型。 |
