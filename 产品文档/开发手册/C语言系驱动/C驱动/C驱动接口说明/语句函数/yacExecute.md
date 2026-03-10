## 功能简介

yacExecute函数用于客户端发起执行yacPrepare函数预编译的SQL的请求，返回YAC_SUCCESS表示执行成功，返回YAC_ERROR表示执行失败。

## 函数声明

```c
yacResult yacExecute(yacHandle hStmt);
```

## 参数说明

| 参数名         | 说明          |
| -------------- | ------------- |
| hStmt (IN/OUT) | SQL信息句柄。 |
