## 功能简介

yacNumParams函数用于获取语句中参数的数量，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacNumParams(YacHandle hStmt,
                       YacUint16* count);
```

## 参数说明

|  参数名| 说明|
| -------------- | ---------------------- |
| hStmt (IN/OUT) | SQL信息句柄。          |
| count (OUT)    | 存储语句中参数的数量。 |
