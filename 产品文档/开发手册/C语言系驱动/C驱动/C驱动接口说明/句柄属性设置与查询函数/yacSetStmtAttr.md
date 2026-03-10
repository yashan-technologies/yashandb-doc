## 功能简介

yacSetStmtAttr函数用于设置SQL信息句柄中的某个属性值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

stmt级别的参数会对当前SQL信息句柄的行为产生影响，请通过yacSetStmtAttr()和[yacGetStmtAttr](yacGetStmtAttr)函数进行配置和获取。

| 参数                           | value对应数据类型 | 属性读写权限（R/W） | 说明                                       |
| ------------------------------ | ----------------- | ------------------- |------------------------------------------|
| YAC_ATTR_PARAMSET_SIZE         | YacUint32         | R/W                 | 该值指定本次执行语句的批量执行行数，最大值为65535。        |
| YAC_ATTR_PARAMSET_SIZE          | YacUint32       | R/W                                  | This value specifies the batch execution row count for the current statement, with a maximum value of 65535. |
| YAC_ATTR_ROWSET_SIZE           | YacUint32         | R/W                 | 该值指定本次Fetch操作的批量获取结果集行数。                 |
| YAC_ATTR_ROWS_FETECHED         | YacUint64         | R                   | 该值返回当前结果集总共已Fetch的行数。                    |
| YAC_ATTR_ROWS_AFFECTED         | YacUint64         | R                   | 该值返回本次执行影响的结果行数。                         |
| YAC_ATTR_CURSOR_EOF            | YacBool           | R                   | 该值返回当前结果集是否已经Fetch结束。                    |
| YAC_ATTR_SQLTYPE               | YacSQLType        | R                   | 该值返回本次执行的SQL语句类型。                        |
| YAC_ATTR_IMPLICIT_RESULT_COUNT | YacUint32         | R                   | 该值返回本次执行可用的隐式结果集个数。                      |
| YAC_ATTR_GET_DATA_SUPPORT      | YacBool           | R/W                 | 该值用于设置或查询当前stmt是否开启结果集缓存以使用yacGetData函数。 |
| YAC_ATTR_ROWS_STATUS           | YacRowStatus[]    | R/W                 | 该值返回批量fetch时每一行数据的状态。                    |
| YAC_ATTR_TIMEOUT            | YacUint64            | R/W         | 该值用于设定当前stmt的SQL执行超时时间。单位：秒，0表示不设置超时。    |

## 函数声明

```c
YacResult yacSetStmtAttr(yacHandle hStmt,
                         yacStmtAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength);
```

## 参数说明

| 参数名         | 说明                         |
| -------------- | ---------------------------- |
| hStmt (IN/OUT) | 连接信息句柄。               |
| attr (IN)      | 属性类型。                   |
| value (IN)     | 欲设置的属性值。             |
| bufLength (IN) | 欲设置的属性值的缓冲区长度。 |
