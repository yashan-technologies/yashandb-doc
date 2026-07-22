## 功能简介

yacNumberRound函数用于根据指定精度和小数位数重新设置YacNumber，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacNumberRound(YacNumber* n,
                         YacInt32 precision,
                         YacInt32 scale);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------- |
| n (IN/OUT)     | 存放number类型的值。 |
| precision (IN) | 精度，取值范围为[1,38]。 |
| scale (IN)     | 小数位数，取值范围为[-84,127]。 |
