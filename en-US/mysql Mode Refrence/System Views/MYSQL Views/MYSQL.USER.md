This view displays the privilege information of all users.

|Field |Type |Description |
| --- | --- | --- |
| HOST | VARCHAR(60) | The name of the server where the database instance is located |
| USER | VARCHAR(64) | The username of the database user |
| SELECT_PRIV | VARCHAR(1) | SELECT privilege |
| INSERT_PRIV | VARCHAR(1) | INSERT privilege |
| UPDATE_PRIV | VARCHAR(1) | UPDATE privilege |
| DELETE_PRIV | VARCHAR(1) | DELETE privilege |
| CREATE_PRIV | VARCHAR(1) | CREATE privilege |
| DROP_PRIV | VARCHAR(1) | DROP privilege |
| RELOAD_PRIV | VARCHAR(1) | RELOAD privilege |
| SHUTDOWN_PRIV | VARCHAR(1) | SHUTDOWN privilege |
| PROCESS_PRIV | VARCHAR(1) | PROCESS privilege |
| FILE_PRIV | VARCHAR(1) | FILE privilege |
| GRANT_PRIV | VARCHAR(1) | GRANT privilege |
| REFERENCES_PRIV | VARCHAR(1) | REFERENCES privilege |
| INDEX_PRIV | VARCHAR(1) | INDEX privilege |
| ALTER_PRIV | VARCHAR(1) | ALTER privilege |
| SHOW_DB_PRIV | VARCHAR(1) | SHOW DB privilege |
| SUPER_PRIV | VARCHAR(1) | SUPER privilege |
| CREATE_TMP_TABLE_PRIV | VARCHAR(1) | CREATE TMP TABLE privilege |
| LOCK_TABLES_PRIV | VARCHAR(1) | LOCK TABLES privilege |
| EXECUTE_PRIV | VARCHAR(1) | EXECUTE privilege |
| REPL_SLAVE_PRIV | VARCHAR(1) | REPL SLAVE privilege |
| REPL_CLIENT_PRIV | VARCHAR(1) | REPL CLIENT privilege |
| CREATE_VIEW_PRIV | VARCHAR(1) | CREATE VIEW privilege |
| SHOW_VIEW_PRIV | VARCHAR(1) | SHOW VIEW privilege |
| CREATE_ROUTINE_PRIV | VARCHAR(1) | CREATE ROUTINE privilege |
| ALTER_ROUTINE_PRIV | VARCHAR(1) | ALTER ROUTINE privilege |
| CREATE_USER_PRIV | VARCHAR(1) | CREATE USER privilege |
| EVENT_PRIV | VARCHAR(1) | EVENT privilege |
| TRIGGER_PRIV | VARCHAR(1) | TRIGGER privilege |
| CREATE_TABLESPACE_PRIV | VARCHAR(1) | CREATE TABLESPACE privilege |
| SSL_TYPE | VARCHAR(0) | SSL/TLS protocol type |
| SSL_CIPHER | VARCHAR(0) | SSL/TLS encryption algorithm or cipher suite |
| X509_ISSUER | VARCHAR(0) | A field in the X.509 certificate used to indicate the identity of the certificate authority (CA) |
| X509_SUBJECT | VARCHAR(0) | A field in the X.509 certificate used to indicate the identity of the certificate holder (Subject) |
| MAX_QUESTIONS | BIGINT | Maximum number of queries |
| MAX_UPDATES | BIGINT | Maximum number of updates |
| MAX_CONNECTIONS | BIGINT | Maximum number of connections (for all users) |
| MAX_USER_CONNECTIONS | BIGINT | Maximum number of connections (for a single user) |
| PLUGIN | VARCHAR(64) | Plugin |
| AUTHENTICATION_STRING | VARCHAR(4000) | Encrypted password or authentication string |
| PASSWORD_EXPIRED | VARCHAR(1) | Whether the password has expired |
| PASSWORD_LAST_CHANGED | TIMESTAMP | The time when the password was last changed |
| PASSWORD_LIFETIME | BIGINT | Validity period of the password |
| ACCOUNT_LOCKED | VARCHAR(1) | Whether the account is locked |