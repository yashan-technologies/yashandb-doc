This view displays historical SQL statement text captured in the AWR repository. This view captures information from V$SQL and is used together with the DBA_HIST_SQLSTAT view.

|  Field  | Type          | Description  |
| --- |-------------| --- |
| DBID | NUMBER      | Database ID |
| SQL_ID | VARCHAR(13) | SQL identifier |
| SQL_TEXT | CLOB        | SQL statement text |
| COMMAND_TYPE | NUMBER      | SQL command type |