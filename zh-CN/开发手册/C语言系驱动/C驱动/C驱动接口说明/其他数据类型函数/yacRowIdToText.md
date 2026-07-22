## 功能简介

yacRowIdToText函数用于将RowId类型转换为String类型，返回YAC_SUCCESS表示转换成功，返回YAC_ERROR表示转换失败。

## 函数声明

```c
YacResult yacRowIdToText(YacRowId* rowId,
                         YacChar* str,
                         YacInt32 bufLength,
                         YacInt32* length);
```

## 参数说明

|  参数名| 说明|
| -------------- | ---------------------------------- |
| rowId (IN)     | YacRowId类型指针。                 |
| str (OUT)      | 字符串buffer地址。                 |
| bufLength (IN) | 字符串buffer长度。                 |
| length (OUT)   | 用于获取字符串实际长度，可为NULL。 |
