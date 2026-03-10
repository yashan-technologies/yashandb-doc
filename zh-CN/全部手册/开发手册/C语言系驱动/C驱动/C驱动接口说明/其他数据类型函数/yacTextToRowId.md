## 功能简介

yacTextToRowId函数用于将String类型转换为RowId类型，返回YAC_SUCCESS表示转换成功，返回YAC_ERROR表示转换失败。

## 函数声明

```c
YacResult yacTextToRowId(YacChar* str,
                         YacInt32 length,
                         YacRowId* rowId);
```

## 参数说明

|  参数名| 说明|
| ----------- | ------------------------------------------ |
| str (IN)    | 字符串buffer地址。                         |
| length (IN) | 字符串实际长度。                           |
| rowId (OUT) | YacRowId类型指针，用于存放输出的YacRowId。 |
