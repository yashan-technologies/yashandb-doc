## 功能简介

yacNumberToInt函数用于获取存储于YacNumber的整数，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacNumberToInt(const YacNumber* number,
                         YacUint32 length,
                         YacUint32 flag,
                         YacPointer rsl);
```

## 参数说明

|  参数名| 说明|
| ------------- |------------------------------------------------------------|
| number (IN)   | 源YacNumber句柄。                                              |
| length (IN)   | 整数的长度（单位：字节）。                                              |
| flag (IN)     | 整数有无符号标记位。YAC_NUMBER_SIGNED表示有符号，YAC_NUMBER_UNSIGNED表示无符号。 |
| rsl (IN/OUT) | 存放整数的指针。                                                   |
