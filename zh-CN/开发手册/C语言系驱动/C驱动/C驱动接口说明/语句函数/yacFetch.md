## 功能简介

yacFetch函数用于客户端发起获取结果集的请求，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
yacResult yacFetch(yacHandle hStmt,
                   yacUint32* rows);
```

## 参数说明

|  参数名| 说明|
| -------------- | ----------------------------------------------- |
| hStmt (IN/OUT) | SQL信息句柄。                                   |
| rows (IN/OUT)  | 指针，其值是此次fetch动作实际获取结果集的行数。 |
