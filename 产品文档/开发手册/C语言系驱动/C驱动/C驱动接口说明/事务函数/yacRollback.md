## 功能简介

yacRollback函数用于客户端发起回滚请求，返回YAC_SUCCESS表示回滚成功，返回YAC_ERROR表示回滚失败。

## 函数声明

```c
yacResult yacRollback(yacHandle hConn);
```

## 参数说明

| 参数名     | 说明           |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |