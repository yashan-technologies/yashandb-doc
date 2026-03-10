## 功能简介

yacCommit函数用于客户端发起提交请求，返回YAC_SUCCESS表示提交成功，返回YAC_ERROR表示提交失败。

## 函数声明

```c
yacResult yacCommit(yacHandle hConn);
```

## 参数说明

| 参数名     | 说明           |
| ---------- | -------------- |
| hConn (IN) | 连接信息句柄。 |