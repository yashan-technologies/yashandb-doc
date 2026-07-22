This view displays all database-level privilege information.

|Field |Type |Description |
| --- | --- | --- |
| HOST | VARCHAR(60) | The name of the server where the database instance is located |
| DB | VARCHAR(64) | The name of the database |
| USER | VARCHAR(64) | The username of the database user |
| SELECT\_PRIV | VARCHAR(1) | SELECT privilege  |
| INSERT\_PRIV | VARCHAR(1) | INSERT privilege  |
| UPDATE\_PRIV | VARCHAR(1) | UPDATE privilege  |
| DELETE\_PRIV | VARCHAR(1) | DELETE privilege  |
| CREATE\_PRIV | VARCHAR(1) | CREATE privilege  |
| DROP\_PRIV | VARCHAR(1) | DROP privilege  |
| GRANT\_PRIV | VARCHAR(1) | GRANT privilege  |
| REFERENCES\_PRIV | VARCHAR(1) | REFERENCES privilege  |
| INDEX\_PRIV | VARCHAR(1) | INDEX privilege  |
| ALTER\_PRIV | VARCHAR(1) | ALTER privilege  |
| CREATE\_TMP\_TABLE\_PRIV | VARCHAR(1) | Create temporary table privilege  |
| LOCK\_TABLES\_PRIV | VARCHAR(1) | LOCK TABLES privilege  |
| CREATE\_VIEW\_PRIV | VARCHAR(1) | CREATE VIEW privilege  |
| SHOW\_VIEW\_PRIV | VARCHAR(1) | SHOW VIEW privilege  |
| CREATE\_ROUTINE\_PRIV | VARCHAR(1) | CREATE ROUTINE privilege  |
| ALTER\_ROUTINE\_PRIV | VARCHAR(1) | ALTER ROUTINE privilege  |
| EXECUTE\_PRIV | VARCHAR(1) | EXECUTE privilege  |
| EVENT\_PRIV | VARCHAR(1) | EVENT privilege  |
| TRIGGER\_PRIV | VARCHAR(1) | TRIGGER privilege  |