## 功能简介

yacDirectExecute函数用于执行客户端发起的SQL请求，支持执行无需绑定数据的SQL语句或者需要绑定参数数据的SQL语句。返回YAC_SUCCESS表示执行成功，返回YAC_ERROR表示执行失败。

## 函数声明

```c
yacResult yacDirectExecute(yacHandle hStmt,
                           const yacChar* sql,
                           YacInt32 sqlLength);
```

## 参数说明

|  参数名| 说明|
| -------------- | -------------------------------------------------- |
| hStmt (IN/OUT) | SQL信息句柄。                                      |
| sql (IN)       | SQL语句字符串。                                    |
| sqlLength (IN) | SQL语句长度。可传入YAC_NULL_TERM_STR表示以\0结尾。 |
