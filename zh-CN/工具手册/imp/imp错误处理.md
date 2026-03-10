## 导入过程错误处理
在导入过程中，由于对象已存在、表约束、系统资源不足等情况可能存在，导致导入过程中出现告警或中断导入操作。

### 行数据导入报错

如果在导入过程中由于约束或数据非法导致无法正常导入该行，会输出告警信息，该表的其余数据继续导入。

约束报错包括如下：

- NOT NULL 约束
- 唯一约束
- 主键约束
- 引用完整性约束
- 列约束

### 对象导入报错

在对象导入时，由于对象已存在等情况会导致无法正常创建对象。主要包括如下场景：

- 对象已存在
- 系统资源报错

#### 对象已存在报错

在导入数据前，若表等对象未被删除，会产生对象已存在的报错。

- 若IGNORE=N，当前数据库原有对象不会被替换，输出对象已存在告警，该对象关联数据不会再被导入。
- 若IGNORE=Y，当前数据库原有对象不会被替换，不会输出告警信息，该对象关联数据会正常导入。如表已存在，则表的数据、索引等会正常导入。

#### 系统资源报错

当前若出现如下报错则终止导入操作。
|  报错内容| 描述|
| --------------------------------------------------- | --- |
| *cannot allocate n bytes from x allocator* | 内存分配器容量不足 |
| *no free blocks in large pool* | 当前并发数太高导致large pool不足 |
| *no free block in x* | 剩余内存大小不足 |
| *no free space in virtual memory pool* | virtual memory pool空间不足 |

在导入过程中非上述报错只输出告警，继续进行导入操作。

## 错误码

### 数据库错误码

由'YAS-'+五位数字组成的错误码的详细信息请参考[数据库错误码](../../参考手册/错误码)。

## imp错误码

错误码编号（Error Number）：由'YASIMP-'+五位数字组成。

错误码消息（Error Message）：错误信息描述，在不同的错误场景下，系统通过变量进行针对该项错误具体信息的消息传递。

### YASIMP-001:EXIM\_ERR\_UNRECOGNIZED\_PARAM

**Message**: unrecognized parameter %s

**Action**：删除无法识别的参数。

### YASIMP-002:EXIM\_ERR\_INVALID\_PARAM\_VALUE

**Message**: invalid value for parameter %s

**Action**：根据信息检查参数。

### YASIMP-003:EXIM\_ERR\_INVALID\_PARAM

**Message**: invalid parameter %s occurs

**Action**：根据信息检查数据参数。

### YASIMP-004:EXIM\_ERR\_PARAMS\_EXPECTED

**Message**: parameter %s is requested

**Action**：根据信息添加必要的参数。

### YASIMP-005:EXIM\_ERR\_INVALID\_USERID

**Message**: invalid %s

**Action**：根据信息检查连接串信息。
