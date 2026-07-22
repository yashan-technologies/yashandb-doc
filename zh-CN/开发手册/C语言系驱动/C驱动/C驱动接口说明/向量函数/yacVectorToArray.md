## 功能简介

yacVectorToArray函数用于将yacVector对象转换为数组。返回YAC_SUCCESS表示转换成功，返回YAC_ERROR表示转换失败。

## 函数声明

```c
YacResult yacVectorToArray(YacVector* vector,
                           YacVectorFormat format,
                           YacUint16* dim,
                           YacUint8* array,
                           YacUint32* arrayLen,
                           YacUint32 mode);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------- |
| vector (IN/OUT) | 用于转换的源vector描述符。 |
| format (IN)     | 指定向量的值类型，可选[YAC_VECTOR_FORMAT_FLOAT32,YAC_VECTOR_FORMAT_FLOAT64]。 |
| dim (IN)        | 指定构造向量的维度值，范围(0，65535]。 |
| dim (OUT)       | 返回转换后数组的元素数量。 |
| array (OUT)     | 接收转换后数组的起始地址。 |
| arrayLen (OUT)  | 返回转换后数组的字节长度。 |
| mode (IN)       | 预留参数。 |
