## 功能简介

yacVectorFromText函数用于从字符串构造yacVector对象。返回YAC_SUCCESS表示构造成功，返回YAC_ERROR表示构造失败。

## 函数声明

```c
YacResult yacVectorFromText(YacVector* vector,
                            YacVectorFormat format,
                            YacUint16 dim,
                            YacChar* text,
                            YacUint32 textlen,
                            YacUint32 mode);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| vector (IN/OUT) | 接收构造的vector的描述符。 |
| format (IN)     | 指定构造向量的值类型，可选[YAC_VECTOR_FORMAT_FLOAT32,YAC_VECTOR_FORMAT_FLOAT64]。 |
| dim (IN)        | 指定构造向量的维度值，范围(0，65535]。 |
| text (IN)       | 用于构造向量的原始文本缓存区的起始地址。 |
| textlen (IN)    | 用于构造向量的原始文本长度。 |
| mode (IN)       | 预留参数。 |
