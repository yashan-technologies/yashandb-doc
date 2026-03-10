## 功能简介

yacPrepare函数用于客户端发起预编译SQL的请求，返回YAC_SUCCESS表示预编译成功，返回YAC_ERROR表示预编译失败。

## 函数声明

```c
yacResult yacPrepare(yacHandle hStmt,
                     const yacChar* sql,
                     YacInt32 sqlLength);
```

## 参数说明

| 参数名         | 说明                                               |
| -------------- | -------------------------------------------------- |
| hStmt (IN/OUT) | SQL信息句柄。                                      |
| sql (IN)       | SQL语句字符串。                                    |
| sqlLength (IN) | SQL语句长度，可传入YAC_NULL_TERM_STR表示以\0结尾。 |