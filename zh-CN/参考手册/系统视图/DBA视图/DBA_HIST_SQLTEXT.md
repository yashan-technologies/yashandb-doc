本视图显示AWR存储库中捕获的历史SQL语句文本。该视图从V$SQL捕获信息，并与DBA_HIST_SQLSTAT视图一起使用。

| 字段  | 类型          | 说明  |
| --- |-------------| --- |
| DBID | NUMBER      | 数据库ID |
| SQL_ID | VARCHAR(13) | SQL标识符 |
| SQL_TEXT | CLOB        | SQL语句文本 |
| COMMAND_TYPE | NUMBER      | SQL命令类型 |