## 功能简介

yacGetData函数用于客户端从已缓存的结果集中获取数据，返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

## 函数声明

```c
YacResult yacGetData(YacHandle hStmt,
                     YacUint16 id,
                     YacUint32 rowNumber,
                     YacUint32 extType,
                     YacPointer value,
                     YacInt32 bufLen,
                     YacInt32* indicator);
```

## 参数说明

| 参数名          | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| hStmt (IN/OUT)  | SQL信息句柄。                                                |
| id (IN)         | 列的编号索引，0表示第1列。                                 |
| rowNumber (IN)  | 要获取的已缓存结果集的行序号，从1开始。                      |
| type (IN)       | 要获取已缓存结果集的列序号。                                 |
| value (IN)      | 要获取已缓存结果集的缓冲区。                                 |
| bufLen (IN)     | 要获取已缓存结果集的缓冲区宽度。                             |
| indicator (OUT) | 辅助指针，其值表示结果集取值情况。值为YAC_NULL_DATA表示取出的数据为NULL，其他值表示取出值的实际大小。<br/>当赋值为NULL时，表示该指针不生效。 |