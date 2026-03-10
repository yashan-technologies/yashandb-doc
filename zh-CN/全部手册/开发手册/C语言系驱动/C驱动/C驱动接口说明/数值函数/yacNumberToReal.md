## 功能简介

yacNumberToReal函数用于获取存储于YacNumber的实数，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacNumberToReal(const YacNumber* number,
                         YacUint32 length,
                         YacPointer rsl);
```

## 参数说明

|  参数名| 说明|
| ------------- |---------------|
| number (IN)  | 源YacNumber句柄。 |
| length (IN)  | 实数的长度（单位：字节）。 |
| rsl (IN/OUT) | 存放实数的指针。      |
