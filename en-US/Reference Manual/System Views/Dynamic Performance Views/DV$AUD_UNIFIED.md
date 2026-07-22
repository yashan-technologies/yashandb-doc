This view displays audit information.

|Field |Type |Description |
| ------------------------ |---------------| --------- |
| GROUP\_ID                | INTEGER        | Group ID                   |
| GROUP\_NODE\_ID          | INTEGER        | Node ID within the group   |
| SESSIONID                | BIGINT         | Audit session ID           |
| OS\_USER                 | VARCHAR(128)   | Operating system username   |
| HOST\_NAME               | VARCHAR(128)   | Server username            |
| INSTANCE\_ID             | SMALLINT       | Instance ID                |
| DBID                     | INTEGER        | Database ID                |
| AUTHENTICATION\_TYPE     | VARCHAR(256)   | Authentication type        |
| DBUSERNAME               | VARCHAR(128)   | Username                   |
| CLIENT\_PROGRAM\_NAME    | VARCHAR(84)    | Client program name        |
| STATEMENT\_ID            | BIGINT        | statement ID |
| EVENT\_TIMESTAMP         | TIMESTAMP(6)      | Timestamp of the event     |
| ACTION                   | INTEGER        | Audit type                 |
| RETURN\_CODE             | INTEGER        | Return code                |
| THREAD\_ID               | BIGINT         | Thread ID                  |
| SCN                      | BIGINT         | System Change Number       |
| SQL\_TEXT                | CLOB           | Original SQL               |
| CURRENT\_USER            | VARCHAR(128)   | Current user               |
| UNIFIED\_AUDIT\_POLICIES | VARCHAR(4000)  | Audit policies             |
| OBJECT\_SCHEMA           | VARCHAR(68)    | Object owner               |
| OBJECT\_NAME             | VARCHAR(68)    | Object name                |
| SYSTEM_PRIVILEGE_USED    | VARCHAR(1024)  | Privilege column           |
| SQL_BINDS                | VARCHAR(4000)  | Bind parameters            |