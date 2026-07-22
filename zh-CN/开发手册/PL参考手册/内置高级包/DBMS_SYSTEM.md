DBMS_SYSTEM包提供了一组内置的存储过程，用于将用户自定义信息写入数据库的trace文件或运行日志，便于用户进行故障诊断、ETL过程监控以及自定义错误告警等。

> **Note**:
>
> 调用本存储过程的用户需要具有`SYS.DBMS_SYSTEM`的`EXECUTE`权限。仅SYS用户可直接使用本存储过程，其他用户需由SYS用户授权后方可使用，例如：`GRANT EXECUTE ON SYS.DBMS_SYSTEM TO <username>;`。


## KSDWRT

```plsql
DBMS_SYSTEM.KSDWRT (
    DEST   IN  INTEGER,
    TEXT   IN  VARCHAR2);
```

KSDWRT为一个存储过程，用于将用户自定义的字符串写入指定的日志目标。

|  参数 | 描述 |
| :--- | :--- |
| DEST | 写入目标，取值为1、2或3，当DEST为NULL或取其他非合法值时，默认按照1处理。<br/>1：仅写入当前会话的trace文件，文件路径为`$YASDB_DATA/log/trace/yashandb_.trc`。<br/>2：仅写入运行日志，文件路径为`$YASDB_DATA/log/run/run.log`。<br/>3：同时写入trace文件和运行日志。 |
| TEXT | 待写入的文本内容，最大长度为65534字节。<br/>当TEXT为NULL或为空字符串时，该存储过程不执行任何操作并直接返回成功。<br/>当TEXT长度大于1000字符时，系统将自动以每1000字符为单位进行截断并为非末尾片段拼接`...`标记后依次写入。 |

执行写入时遵循以下原则：

- 系统会对写入内容进行格式化，并拼接时间戳、进程号、会话标识等信息，完整格式为`<timestamp> <pid> [INFO] SESSION#<sid>_<serial#> <content>`。
- 多会话并发写入同一日志目标时，系统通过文件锁保证写入数据的完整性和顺序，不会出现丢失或乱序。

示例

```plsql
-- DEST=1，将文本写入当前会话的trace文件
BEGIN
  DBMS_SYSTEM.KSDWRT(1, 'Begin batch process: ' || TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS'));
END;
/

-- DEST=2，将文本写入运行日志
BEGIN
  DBMS_SYSTEM.KSDWRT(2, 'Custom alert: order processing completed.');
END;
/

-- DEST=3，同时将文本写入trace文件和运行日志
BEGIN
  DBMS_SYSTEM.KSDWRT(3, 'Critical event triggered.');
END;
/
```
