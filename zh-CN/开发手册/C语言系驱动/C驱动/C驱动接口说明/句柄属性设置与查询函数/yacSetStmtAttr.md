## 功能简介

yacSetStmtAttr函数用于设置sql信息句柄中的某个属性值，返回YAC_SUCCESS表示设置成功，返回YAC_ERROR表示设置失败。

stmt级别的参数会对当前sql信息句柄的行为产生影响，请通过yacSetStmtAttr()和[yacGetStmtAttr](yacGetStmtAttr)函数进行配置和获取。

| 属性 | value对应数据类型 | 属性读写权限（R/W） | 说明 |
| -- | -- | -- | -- |
| YAC_ATTR_PARAMSET_SIZE | YacUint32 | R/W | 参数集大小，设置批量操作中参数集的数量，最大值为65535 |
| YAC_ATTR_ROWSET_SIZE | YacUint32 | R/W | 行集大小，设置每次获取的行数 |
| YAC_ATTR_IS_BATCH_ERRORS | YacBool | R/W | 批量错误模式，是否支持批量操作中的错误继续执行 |
| YAC_ATTR_IS_BATCHROWS | YacBool | R/W | 批量行模式，是否启用批量行处理 |
| YAC_ATTR_IS_BULK_LOAD | YacBool | R/W | 批量加载模式，是否启用bulk load高效批量插入 |
| YAC_ATTR_IS_DEDUP | YacBool | R/W | 去重模式，是否对数据进行去重处理 |
| YAC_ATTR_GET_DATA_SUPPORT | YacBool | R/W | 获取数据支持，是否允许使用SQLGetData获取数据（若有已取行数则不允许开启） |
| YAC_ATTR_ROWS_FETCHED | YacUint64 | R | 已获取行数，只读属性，返回当前结果集总共已Fetch的行数 |
| YAC_ATTR_ROWS_AFFECTED | YacUint64 | R | 受影响的行数，只读属性，返回本次执行影响的结果行数 |
| YAC_ATTR_CURSOR_EOF | YacBool | R | 游标结束标志，只读属性，返回当前结果集是否已经Fetch结束 |
| YAC_ATTR_IS_DSTB_PART | YacBool | R/W | DSTB分区模式，是否启用分布式分区处理 |
| YAC_ATTR_IS_SINGLE_PART | YacBool | R/W | 单分区模式，是否限制操作在单个分区执行 |
| YAC_ATTR_ROWS_STATUS | YacRowStatus[] | R/W | 行状态数组指针，指向存储每行执行状态的结构体数组 |
| YAC_ATTR_PART_NUM | YacUint64 | R/W | 分区号，指定操作的目标分区编号 |
| YAC_ATTR_TIMEOUT | YacUint64 | R/W | 超时时间（毫秒），设置语句执行的最大等待时间，必须 >= 0 |
| YAC_ATTR_SQLTYPE | YacSQLType | R | SQL语句类型，只读属性，获取当前语句的类型 |
| YAC_ATTR_IMPLICIT_RESULT_COUNT | YacUint32 | R | 隐式结果集个数，只读属性，获取本次执行可用的隐式结果集数量 |

## 函数声明

```c
YacResult yacSetStmtAttr(yacHandle hStmt,
                         yacStmtAttr attr,
                         YacVoid* value,
                         YacInt32 bufLength);
```

## 参数说明

|  参数名| 说明|
| -------------- | ---------------------------- |
| hStmt (IN/OUT) | 连接信息句柄。               |
| attr (IN)      | 属性类型。                   |
| value (IN)     | 欲设置的属性值。             |
| bufLength (IN) | 欲设置的属性值的缓冲区长度。 |
