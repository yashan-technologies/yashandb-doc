This view displays all database-level privilege information.

|Field |Type |Description |
| --- | --- | --- |
| HOST | VARCHAR(60) | The name of the server where the database instance is located |
| DB | VARCHAR(64) | The name of the database |
| USER | VARCHAR(64) | The username of the database user |
| SELECT_PRIV | VARCHAR(1) | SELECT privilege  |
| INSERT_PRIV | VARCHAR(1) | INSERT privilege  |
| UPDATE_PRIV | VARCHAR(1) | UPDATE privilege  |
| DELETE_PRIV | VARCHAR(1) | DELETE privilege  |
| CREATE_PRIV | VARCHAR(1) | CREATE privilege  |
| DROP_PRIV | VARCHAR(1) | DROP privilege  |
| GRANT_PRIV | VARCHAR(1) | GRANT privilege  |
| REFERENCES_PRIV | VARCHAR(1) | REFERENCES privilege  |
| INDEX_PRIV | VARCHAR(1) | INDEX privilege  |
| ALTER_PRIV | VARCHAR(1) | ALTER privilege  |
| CREATE_TMP_TABLE_PRIV | VARCHAR(1) | Create temporary table privilege  |
| LOCK_TABLES_PRIV | VARCHAR(1) | LOCK TABLES privilege  |
| CREATE_VIEW_PRIV | VARCHAR(1) | CREATE VIEW privilege  |
| SHOW_VIEW_PRIV | VARCHAR(1) | SHOW VIEW privilege  |
| CREATE_ROUTINE_PRIV | VARCHAR(1) | CREATE ROUTINE privilege  |
| ALTER_ROUTINE_PRIV | VARCHAR(1) | ALTER ROUTINE privilege  |
| EXECUTE_PRIV | VARCHAR(1) | EXECUTE privilege  |
| EVENT_PRIV | VARCHAR(1) | EVENT privilege  |
| TRIGGER_PRIV | VARCHAR(1) | TRIGGER privilege  |