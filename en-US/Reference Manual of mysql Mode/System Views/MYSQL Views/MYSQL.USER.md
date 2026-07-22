This view displays the privilege information of all users.

|Field |Type |Description |
| --- | --- | --- |
| HOST | VARCHAR(60) | The name of the server where the database instance is located |
| USER | VARCHAR(64) | The username of the database user |
| SELECT\_PRIV | VARCHAR(1) | SELECT privilege |
| INSERT\_PRIV | VARCHAR(1) | INSERT privilege |
| UPDATE\_PRIV | VARCHAR(1) | UPDATE privilege |
| DELETE\_PRIV | VARCHAR(1) | DELETE privilege |
| CREATE\_PRIV | VARCHAR(1) | CREATE privilege |
| DROP\_PRIV | VARCHAR(1) | DROP privilege |
| RELOAD\_PRIV | VARCHAR(1) | RELOAD privilege |
| SHUTDOWN\_PRIV | VARCHAR(1) | SHUTDOWN privilege |
| PROCESS\_PRIV | VARCHAR(1) | PROCESS privilege |
| FILE\_PRIV | VARCHAR(1) | FILE privilege |
| GRANT\_PRIV | VARCHAR(1) | GRANT privilege |
| REFERENCES\_PRIV | VARCHAR(1) | REFERENCES privilege |
| INDEX\_PRIV | VARCHAR(1) | INDEX privilege |
| ALTER\_PRIV | VARCHAR(1) | ALTER privilege |
| SHOW\_DB\_PRIV | VARCHAR(1) | SHOW DB privilege |
| SUPER\_PRIV | VARCHAR(1) | SUPER privilege |
| CREATE\_TMP\_TABLE\_PRIV | VARCHAR(1) | CREATE TMP TABLE privilege |
| LOCK\_TABLES\_PRIV | VARCHAR(1) | LOCK TABLES privilege |
| EXECUTE\_PRIV | VARCHAR(1) | EXECUTE privilege |
| REPL\_SLAVE\_PRIV | VARCHAR(1) | REPL SLAVE privilege |
| REPL\_CLIENT\_PRIV | VARCHAR(1) | REPL CLIENT privilege |
| CREATE\_VIEW\_PRIV | VARCHAR(1) | CREATE VIEW privilege |
| SHOW\_VIEW\_PRIV | VARCHAR(1) | SHOW VIEW privilege |
| CREATE\_ROUTINE\_PRIV | VARCHAR(1) | CREATE ROUTINE privilege |
| ALTER\_ROUTINE\_PRIV | VARCHAR(1) | ALTER ROUTINE privilege |
| CREATE\_USER\_PRIV | VARCHAR(1) | CREATE USER privilege |
| EVENT\_PRIV | VARCHAR(1) | EVENT privilege |
| TRIGGER\_PRIV | VARCHAR(1) | TRIGGER privilege |
| CREATE\_TABLESPACE\_PRIV | VARCHAR(1) | CREATE TABLESPACE privilege |
| SSL\_TYPE | VARCHAR(0) | SSL/TLS protocol type |
| SSL\_CIPHER | VARCHAR(0) | SSL/TLS encryption algorithm or cipher suite |
| X509\_ISSUER | VARCHAR(0) | A field in the X.509 certificate used to indicate the identity of the certificate authority (CA) |
| X509\_SUBJECT | VARCHAR(0) | A field in the X.509 certificate used to indicate the identity of the certificate holder (Subject) |
| MAX\_QUESTIONS | BIGINT | Maximum number of queries |
| MAX\_UPDATES | BIGINT | Maximum number of updates |
| MAX\_CONNECTIONS | BIGINT | Maximum number of connections (for all users) |
| MAX\_USER\_CONNECTIONS | BIGINT | Maximum number of connections (for a single user) |
| PLUGIN | VARCHAR(64) | Plugin |
| AUTHENTICATION\_STRING | VARCHAR(4000) | Encrypted password or authentication string |
| PASSWORD\_EXPIRED | VARCHAR(1) | Whether the password has expired |
| PASSWORD\_LAST\_CHANGED | TIMESTAMP | The time when the password was last changed |
| PASSWORD\_LIFETIME | BIGINT | Validity period of the password |
| ACCOUNT\_LOCKED | VARCHAR(1) | Whether the account is locked |