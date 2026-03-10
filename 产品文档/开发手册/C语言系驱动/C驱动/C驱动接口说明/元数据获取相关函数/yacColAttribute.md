## 功能简介

yacColAttribute函数用于获取SQL信息句柄中的某个列属性值，具体属性值信息如下表所示。返回YAC_SUCCESS表示获取成功，返回YAC_ERROR表示获取失败。

| 属性                         | value对应数据类型 | 说明                     |
| ------------------------------ | ----------------- | ------------------------ |
| YAC_COL_ATTR_DISPLAY_SIZE      | YacUint32         | 列显示大小               |
| YAC_COL_ATTR_DISPLAY_CHAR_SIZE | YacUint32         | 列显示字符大小           |
| YAC_COL_ATTR_NAME              | YarChar*          | 列名                     |
| YAC_COL_ATTR_SIZE              | YacUint32         | 列大小                   |
| YAC_COL_ATTR_CHAR_SIZE         | YacUint32         | 列字符大小               |
| YAC_COL_ATTR_TYPE              | YacUint8          | 列类型                   |
| YAC_COL_ATTR_PRECISION         | YacUint8          | 列精度                   |
| YAC_COL_ATTR_SCALE             | YacInt8           | 列范围                   |
| YAC_COL_ATTR_NULLABLE          | YacUint8          | 列是否可为NULL           |
| YAC_COL_ATTR_CHAR_USED         | YacUint8          | 类型定义是否为字符长定义 |

## 函数声明

```c
YacResult yacColAttribute(YacHandle hStmt,
                          YacUint16 id,
                          YacColAttr attr,
                          YacVoid* value,
                          YacInt32 bufLen,
                          YacInt32* stringLength);
```

## 参数说明

| 参数名                | 说明                         |
| --------------------- | ---------------------------- |
| hStmt (IN/OUT)        | SQL信息句柄。                |
| id (IN)               | 列的编号索引，0表示第1列。 |
| attr (IN)             | 属性类型。                   |
| value (OUT)           | 存储获取到的属性值。         |
| bufLen (OUT)          | 获取到的属性值的存储区长度。 |
| stringLength (IN/OUT) | 实际获取到的字符串长度。     |