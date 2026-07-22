## 功能简介

yacNumberFromInt函数用于将整数赋给YacNumber，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

## 函数声明

```c
YacResult yacNumberFromInt(const YacPointer inum,
                         YacUint32 length,
                         YacUint32 flag,
                         YacNumber* number);
```

## 参数说明

|  参数名| 说明|
|-----------------|------------------------------------------------------------|
| inum (IN)       | 指向要转换的整数的指针。                                                      |
| length (IN)     | 整数的长度（单位：字节）。                                              |
| flag (IN)       | 整数有无符号标记位。YAC_NUMBER_SIGNED表示有符号，YAC_NUMBER_UNSIGNED表示无符号。 |
| number (IN/OUT) | 要被修改的YacNumber句柄。                                          |
