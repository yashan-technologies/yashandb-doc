Flashback refers to the ability to revert the database objects to a historical point in time by rolling back only the relevant changes without performing traditional restore operations from backups.

YashanDB supports flashback recovery [FLASHBACK](../../Development Guide/SQL Reference Manual/SQL Statements/FLASHBACK) for the entire database or specific tables. For example, it can flash back to a point in time before a DROP operation on a table to restore accidentally deleted tables and data. The flashback operation does not affect database usage and does not incur additional space consumption.

Additionally, executing the SELECT statement with the [flashback_query_clause](../../Development Guide/SQL Reference Manual/SQL Statements/SELECT.html#flashbackqueryclause) clause enables flashback queries, allowing users to trace historical versions of table data. Under certain conditions, users can still access historical data of a table even after it has been modified with UPDATE or DELETE operations.

For detailed operational procedures regarding flashback, please refer to [Flashback](../../Database Administration/Flashback/00Flashback).