This view displays audit information.

|Field |Type |Description |
|--------------------------|---------------|--------------|
| GROUP_ID                 | NUMBER         | Group ID        |
| GROUP_NODE_ID            | NUMBER         | Node ID within the group |
| INST_ID                  | NUMBER         | Instance ID     |
| SESSIONID                | BIGINT         | Audit session ID |
| OS_USER                  | VARCHAR(128)   | Operating system username |
| HOST_NAME                | VARCHAR(128)   | Server username  |
| DBID                     | INTEGER        | Database ID     |
| AUTHENTICATION_TYPE      | VARCHAR(256)   | Authentication type |
| DBUSERNAME               | VARCHAR(128)   | Username        |
| CLIENT_PROGRAM_NAME       | VARCHAR(84)    | Client program name |
| STATEMENT_ID            | BIGINT        | statement ID |
| EVENT_TIMESTAMP          | TIMESTAMP      | Event occurrence time |
| ACTION                   | INTEGER        | Audit type      |
| RETURN_CODE              | INTEGER        | Return code     |
| THREAD_ID                | BIGINT         | Thread ID       |
| SCN                      | BIGINT         | System change number |
| SQL_TEXT                 | CLOB           | Original SQL    |
| CURRENT_USER             | VARCHAR(128)   | Current user    |
| UNIFIED_AUDIT_POLICIES   | VARCHAR(4000)  | Audit policies   |
| OBJECT_SCHEMA            | VARCHAR(68)    | Object owner    |
| OBJECT_NAME              | VARCHAR(68)    | Object name     |
| SYSTEM_PRIVILEGE_USED    | VARCHAR(1024)  | Privilege column |
| SQL_BINDS                | VARCHAR(4000)  | Bound parameters |