## 功能简介

yacStmtGetNextResult函数用于获取隐式结果集，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacStmtGetNextResult(YacHandle  hStmt,
                               YacHandle* cursor,
                               YacUint32* rtType);
```

## 参数说明

| 参数名         | 说明                                                 |
| -------------- | ---------------------------------------------------- |
| hStmt (IN/OUT) | SQL信息句柄。                                        |
| cursor(OUT)    | 服务端通过dbms_sql.return_result返回给客户端的游标。 |
| rtType (OUT)   | 隐式结果集类型，目前固定返回YAC_RESULT_TYPE_SELECT。 |