This view displays audit record information.

|Field |Type |Description |
| --- | --- | --- |
| SESSIONID | BIGINT | Audit session ID |
| OS_USER | VARCHAR(128) | Operating system user name of the database session |
| HOST_NAME | VARCHAR(128) | Primary database name |
| INSTANCE_ID | SMALLINT | Instance ID |
| DBID | INTEGER | Database ID |
| AUTHENTICATION_TYPE | VARCHAR(256) | Authentication information for the session user |
| DBUSERNAME | VARCHAR(128) | Login user name |
| CLIENT_PROGRAM_NAME | VARCHAR(84) | Client name |
| STATEMENT_ID | BIGINT | Statement ID, no actual meaning |
| EVENT_TIMESTAMP | TIMESTAMP | Time |
| ACTION | VARCHAR(64) | Name of the audit item |
| RETURN_CODE | INTEGER | Error code value, 0 if the corresponding audit event execution is successful |
| THREAD_ID | BIGINT | Thread ID |
| SCN | BIGINT | SCN |
| SQL_TEXT | CLOB | SQL content |
| CURRENT_USER | VARCHAR(128) | Current user executing the statement |
| UNIFIED_AUDIT_POLICIES | VARCHAR(4000) | Name of the audit policy |
| NODE_NAME | VARCHAR(68) | Physical node name, recorded as "unknown" for standalone |
| OBJECT_SCHEMA | VARCHAR(68) | User name of the audited object's owner |
| OBJECT_NAME | VARCHAR(68) | Name of the audited object |
| SYSTEM_PRIVILEGE_USED | VARCHAR(1024) | Privilege name |
| SQL_BINDS | VARCHAR(4000) | Values of binding parameters |
| TRANSACTION_ID | BIGINT | Transaction ID |
| ROLE | VARCHAR(64) | Role type of the login user |
| YLS_POLICY_NAME | VARCHAR(64) | Row access control policy name |
| YLS_GRANTEE | VARCHAR(64) | User applying the row access control policy |
| YLS_MAX_READ_LABEL | VARCHAR(4000) | User's maximum read label |
| YLS_MAX_WRITE_LABEL | VARCHAR(4000) | User's maximum write label |
| YLS_MIN_WRITE_LABEL | VARCHAR(64) | User's minimum write label |
| YLS_STRING_LABEL | VARCHAR(4000) | Label content |
| YLS_LABEL_COMPONENT_TYPE | VARCHAR(12) | Component type |
| YLS_LABEL_COMPONENT_NAME | VARCHAR(64) | Component name |
| RLS_INFO | CLOB | Row access control policy name associated with the executed statement |