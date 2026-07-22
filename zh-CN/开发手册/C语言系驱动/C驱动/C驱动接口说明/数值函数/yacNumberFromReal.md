## 功能简介

yacNumberFromReal函数用于将实数赋给YacNumber，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacNumberFromReal(const YacPointer rnum,
                         YacUint32 length,
                         YacNumber* number);
```

## 参数说明

|  参数名| 说明|
| -------------- |-------------------|
| rnum (IN)       | 指向要转换的实数的指针。      |
| length (IN)     | 实数的长度（单位：字节）。     |
| number (IN/OUT) | 要被修改的YacNumber句柄。 |
