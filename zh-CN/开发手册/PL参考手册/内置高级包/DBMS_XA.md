DBMS_XA包提供了一组常量、数据结构和函数，用于在PL中调用XA接口。

DBMS_XA包仅适用于单机部署。

DBMS_XA包不能对LSC表使用。

使用DBMS_XA包的数据结构和函数需要EXECUTE ANY TYPE和EXECUTE ANY PROCEDURE系统权限。

## DBMS\_XA数据结构

DBMS_XA包中使用自定义类型数据结构作为函数参数或返回值。

### DBMS\_XA\_XID OBJECT TYPE

```plsql
TYPE DBMS_XA_XID IS OBJECT(
    formatid      NUMBER,
    gtrid         RAW(64),
    bqual         RAW(64),
    constructor function DBMS_XA_XID(
        formatid  IN   NUMBER,
        gtrid     IN   RAW,
        bqual     IN   RAW)
    RETURN SELF AS RESULT)
```

YashanDB提供的DBMS_XA_XID是一种UDT类型主体，使用构造函数时必须输入全部3种参数，参数描述如下：

|  参数| 描述|
| :------------------- | :--------------------------------------------------------------------------------------|
| formatid             | 格式标识符，兼容参数，无实际意义                                                          |
| gtrid                | 全局标识符，唯一区分全局事务，最大为64字节，字段末尾的0会被忽略（即'12300100'等同于'123001'）|
| bqual                | 分支限定符，最大为64字节，字段末尾的0会被忽略（即'12300100'等同于'123001'）                 |

### DBMS\_XA\_XID\_ARRAY TABLE TYPE

YashanDB提供的DBMS_XA_XID_ARRAY是一种嵌套表类型的UDT。

```plsql
TYPE DBMS_XA_XID_ARRAY as TABLE of DBMS_XA_XID
```

## DBMS\_XA常量

DBMS_XA高级包中定义了一些常量，用作函数参数或返回值。

下表列示了可作为XA_START和XA_END函数的参数使用的常量。

|  名称| 类型| 数值| 描述|
| :------------------- | :----------------| :------------------| :--------------------------------|
| TMNOFLAGS            | PLS_INTEGER      | 00000000           | 表示未选择标志位值                 |
| TMSUCCESS            | PLS_INTEGER      | 0x04000000         | 挂起事务分支并取消调用者和事务分支的关联 |
| TMJOIN               | PLS_INTEGER      | 0x00200000         | 调用者加入已存在的事务分支          |
| TMSUSPEND            | PLS_INTEGER      | 0x02000000         | 仅挂起当前事务分支                 |
| TMRESUME             | PLS_INTEGER      | 0x08000000         | 等同于TMJOIN                    |

下表列示了DBMS_XA包中函数可能的常量返回值。

|  名称| 类型| 数值| 描述|
| :------------------- | :----------------| :----------| :--------------------------------|
| XA_OK                | PLS_INTEGER      | 0          | 正常执行                         |
| XAER_ASYNC           | PLS_INTEGER      | -2         | 异步操作未完成                    |
| XAER_RMERR           | PLS_INTEGER      | -3         | 事务分支中资源管理器报错           |
| XAER_NOTA            | PLS_INTEGER      | -4         | XID无效                          |
| XAER_INVAL           | PLS_INTEGER      | -5         | 参数无效                         |
| XAER_PROTO           | PLS_INTEGER      | -6         | 调用上下文关系不恰当              |
| XAER_RMFAIL          | PLS_INTEGER      | -7         | 资源管理器当前不可用              |
| XAER_DUPID           | PLS_INTEGER      | -8         | 已存在指定的XID                   |
| XAER_OUTSIDE         | PLS_INTEGER      | -9         | 资源管理器正在执行全局事务以外的工作 |
| XAER_PXACT | PLS_INTEGER | -10 | 并行事务不能转成XA事务 |

## DBMS\_XA函数

### XA\_START

```plsql
DBMS_XA.XA_START(
    xid IN DBMS_XA_XID,
    flag IN INTEGER)
RETURN PLS_INTEGER;
```

XA_START函数用于关联当前会话和xid指定的事务分支。

XA_START函数的flag参数只能为TMNOFLAGS、TMJOIN或TMRESUME中的一种。若flag为TMNOFLAGS，将启动一个新的事务分支，否则，当前会话将关联已存在的事务分支。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |
| flag                 | 标志位常量。                                                   |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_START函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_START(xid, DBMS_XA.TMNOFLAGS);
END;
/
```

### XA\_END

```plsql
DBMS_XA.XA_END(
    xid IN DBMS_XA_XID,
    flag IN INTEGER)
RETURN PLS_INTEGER;
```

XA_END函数用于结束当前会话和xid指定的事务分支关联。

XA_END函数的flag参数只能为TMNOFLAGS、TMSUCCESS或TMSUSPEND中的一种，TMNOFLAGS等价于TMSUSPEND。

函数成功返回后，事务分支仍存在。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |
| flag                 | 标志位常量。                                                   |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_END函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_END(xid, DBMS_XA.TMNOFLAGS);
END;
/
```

### XA\_PREPARE

```plsql
DBMS_XA.XA_PREPARE(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

XA_PREPARE函数用于准备提交xid指定的事务分支。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_PREPARE函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_PREPARE(xid);
END;
/
```

### XA\_COMMIT

```plsql
DBMS_XA.XA_COMMIT(
    xid IN DBMS_XA_XID,
    onePhase IN BOOLEAN)
RETURN PLS_INTEGER;
```

XA_COMMIT函数用于提交xid指定的事务分支。

若onePhase参数值为TRUE，资源管理器应使用一阶段协议来代表xid进行提交。

XA事务中不能执行COMMIT语句。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |
| onePhase             | 指定是否应用一阶段提交。                                         |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_COMMIT函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_COMMIT(xid, false);
END;
/
```

### XA\_RECOVER

```plsql
DBMS_XA.XA_RECOVER
RETURN DBMS_XA_XID_ARRAY;
```

XA_RECOVER函数用于从资源管理器（RM）获得已准备好的或完成的事务分支列表。

formatid查询结果恒为0。

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID_ARRAY类型变量并调用XA_RECOVER函数
DECLARE
    xid_array DBMS_XA_XID_ARRAY;
BEGIN
    xid_array := DBMS_XA.XA_RECOVER();
END;
/
```

### XA\_FORGET

```plsql
DBMS_XA.XA_FORGET(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

XA_FORGET函数用于通知资源管理器（RM）丢弃提交或回滚的事务分支。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_FORGET函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_FORGET(xid);
END;
/
```

### XA\_ROLLBACK

```plsql
DBMS_XA.XA_ROLLBACK(
    xid IN DBMS_XA_XID)
RETURN PLS_INTEGER;
```

XA_ROLLBACK函数用于通知资源管理器（RM）回滚事务分支。

XA事务中不建议执行ROLLBACK语句。

|  参数| 描述|
| :------------------- | :-----------------------------------------------------------|
| xid                  | 指定事务分支。                                              |

示例（单机部署）

```plsql
-- 声明DBMS_XA_XID类型变量并调用XA_ROLLBACK函数
DECLARE
    ret INT;
    xid DBMS_XA_XID := DBMS_XA_XID(1, HEXTORAW('1'), HEXTORAW('1'));
BEGIN
    ret := DBMS_XA.XA_ROLLBACK(xid);
END;
/
```
