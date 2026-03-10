System privileges are privileges that allow users to perform certain system-level operations. They may apply to operations related to a certain type of object or to operations related to the entire database. The functionality of system privileges is extensive; therefore, authorization should be granted cautiously. Typically, system privileges are granted only by administrators to ensure that the grantee is a trusted user or role.

You can view all authorized system privilege information currently in the system through the DBA_SYS_PRIVS view.

System privileges include:

- System management privileges: Manage database, tablespace, and session operations.

- Security management privileges: Manage users, roles, and authorization operations.

- Object operation privileges: Manage objects such as tables, indexes, views, sequences, synonyms, UDT, stored procedures, triggers, custom libraries, database links, and materialized views.

- Other system privileges: Manage store outlines, statistics, profiles, logical logs, and import privileges.

## System Management Privileges

|Privilege Name |Description |
| --- | --- |
| ALL PRIVILEGES                | Has all system privileges                              |
| **Database and Session**:      |                                                        |
| ALTER SYSTEM                   | Has the privilege to change system-level parameters and settings |
| ALTER DATABASE                 | Has the privilege to change database-level parameters and settings |
| ALTER SESSION                  | Has the privilege to change session-level parameters and settings |
| CREATE SESSION                 | Has the privilege for user login                       |
| AUDIT SYSTEM                   | Has the privilege to create, modify, enable, disable, and delete audit policies |
| **Tablespace**:                |                                                        |
| CREATE TABLESPACE              | Has the privilege to create tablespaces                |
| ALTER TABLESPACE               | Has the privilege to modify tablespace attributes      |
| DROP TABLESPACE                | Has the privilege to delete tablespaces                |
| UNLIMITED TABLESPACE           | Has the privilege to allocate unlimited space in any tablespace, provided space is reserved for other users; this privilege cannot be granted to roles |

## Security Management Privileges

|Privilege Name |Description |
| --------------------------- | --------------------------------- |
| **Privilege Management**:         |                                                         |
| GRANT ANY PRIVILEGE               | Has the privilege to grant any system privilege         |
| GRANT ANY OBJECT PRIVILEGE        | * Can grant any object privilege, acting on behalf of the object owner <br/> * Can revoke object privileges acting on behalf of the object owner |
| GRANT ANY ROLE                    | Has the privilege to grant any role                     |
| **Users and Roles**:             |                                                         |
| CREATE USER                       | Has the privilege to create users                       |
| ALTER USER                        | Has the privilege to change user attributes             |
| DROP USER                         | Has the privilege to delete users                       |
| CREATE ROLE                       | Has the privilege to create user-defined roles          |
| DROP ANY ROLE                     | Has the privilege to delete user-defined roles          |

## Object Operation Privileges

|Privilege Name |Description |
|------------------------------| --------------------------- |
| **Tables**:                        |                                                         |
| INSERT ANY TABLE                 | Has the privilege to insert data into any table in the database (except sys schema) |
| SELECT ANY TABLE                 | Has the privilege to query any table, view, dynamic view, and materialized view in the database (except sys schema); dynamic views are currently unrestricted |
| UPDATE ANY TABLE                 | Has the privilege to update data in any table in the database (except sys schema) |
| DELETE ANY TABLE                 | Has the privilege to delete data from any table in the database (except sys schema) |
| READ ANY TABLE                   | Has the privilege to query any table in the database (except sys schema), but cannot query with locks |
| CREATE TABLE                     | Has the privilege to create a table in the user's own schema |
| CREATE ANY TABLE                 | Has the privilege to create tables in any schema (except sys schema) |
| ALTER ANY TABLE                  | Has the privilege to perform ALTER operations on any table in the database (except sys schema) |
| DROP ANY TABLE                   | Has the privilege to delete the definition of any table in the database (except sys schema) |
| FLASHBACK ANY TABLE              | Has the privilege to perform flashback queries on any table in the database (except sys schema) |
| COMMENT ANY TABLE                | Has the privilege to comment on any table in the database (except sys schema) |
| **Indexes**:                      |                                                         |
| CREATE ANY INDEX                 | Has the privilege to create indexes on any table in the database (except sys schema) |
| ALTER ANY INDEX                  | Has the privilege to modify properties of any index in the database (except sys schema) |
| DROP ANY INDEX                   | Has the privilege to delete any index in the database (except sys schema) |
| **Sequences**:                    |                                                         |
| CREATE SEQUENCE                  | Has the privilege to create a sequence in the user's own schema |
| CREATE ANY SEQUENCE              | Has the privilege to create sequences in any schema (except sys schema) |
| ALTER ANY SEQUENCE               | Has the privilege to modify properties of any sequence in the database (except sys schema) |
| DROP ANY SEQUENCE                | Has the privilege to delete any sequence in the database (except sys schema) |
| SELECT ANY SEQUENCE              | Has the privilege to query any sequence in the database (except sys schema) |
| **Synonyms**:                     |                                                         |
| CREATE SYNONYM                   | Has the privilege to create a private synonym in the user's own schema |
| CREATE ANY SYNONYM               | Has the privilege to create private synonyms in any schema (except sys schema) |
| CREATE PUBLIC SYNONYM            | Has the privilege to create public synonyms             |
| DROP ANY SYNONYM                 | Has the privilege to delete any private synonym (except sys schema) |
| DROP PUBLIC SYNONYM              | Has the privilege to delete public synonyms             |
| **Views**:                        |                                                         |
| CREATE VIEW                      | Has the privilege to create views in the user's own schema |
| CREATE ANY VIEW                  | Has the privilege to create views in any schema (except sys schema) |
| DROP ANY VIEW                    | Has the privilege to delete any view                    |
| **UDT**:                          |                                                         |
| CREATE TYPE                      | Has the privilege to create UDT in the user's own schema |
| CREATE ANY TYPE                  | Has the privilege to create UDT in any schema (except sys schema) |
| ALTER ANY TYPE                   | Has the privilege to modify attributes of UDT in any schema (except sys schema) |
| DROP ANY TYPE                    | Has the privilege to delete UDT in any schema (except sys schema) |
| EXECUTE ANY TYPE                 | Has the privilege to execute any UDT (except sys schema) |
| UNDER ANY TYPE                   | Has the privilege to create a subtype under a non-final OBJECT type |
| **Stored Procedures**:           |                                                         |
| CREATE PROCEDURE                 | Has the privilege to create a procedure or function in the user's own schema |
| CREATE ANY PROCEDURE             | Has the privilege to create a procedure or function in any schema (except sys schema) |
| ALTER ANY PROCEDURE              | Has the privilege to modify attributes of procedure or function in any schema (except sys schema) |
| DROP ANY PROCEDURE               | Has the privilege to delete any procedure or function (except sys schema) |
| EXECUTE ANY PROCEDURE            | Has the privilege to execute any procedure or function (except sys schema) |
| **Triggers**:                    |                                                         |
| CREATE TRIGGER                   | Has the privilege to create triggers in the user's own schema |
| CREATE ANY TRIGGER               | Has the privilege to create triggers in any schema (except sys schema) |
| ALTER ANY TRIGGER                | Has the privilege to modify attributes of triggers in any schema (except sys schema) |
| DROP ANY TRIGGER                 | Has the privilege to delete triggers in any schema (except sys schema) |
| ADMINISTER DATABASE TRIGGER      | Has the privilege to manage database-level triggers     |
| **Custom Libraries**:            |                                                         |
| CREATE LIBRARY                   | Has the privilege to create custom libraries in the user's own schema |
| CREATE ANY LIBRARY               | Has the privilege to create custom libraries in any schema |
| DROP ANY LIBRARY                 | Has the privilege to delete any custom library (except sys schema) |
| EXECUTE ANY LIBRARY              | Has the privilege to use any custom library when creating external UDF (except sys schema) |
| **Database Links**:              |                                                         |
| CREATE DATABASE LINK             | Has the privilege to create private database links     |
| CREATE PUBLIC DATABASE LINK      | Has the privilege to create public database links      |
| ALTER DATABASE LINK              | Has the privilege to modify private database links     |
| ALTER PUBLIC DATABASE LINK       | Has the privilege to modify public database links      |
| DROP DATABASE LINK               | Has the privilege to delete private database links     |
| DROP PUBLIC DATABASE LINK        | Has the privilege to delete public database links      |
| **Materialized Views**:          |                                                         |
| CREATE MATERIALIZED VIEW         | Has the privilege to create materialized views in the user's own schema |
| CREATE ANY MATERIALIZED VIEW     | Has the privilege to create materialized views in any schema (except sys schema) |
| ALTER ANY MATERIALIZED VIEW      | Has the privilege to perform ALTER operations on any materialized view in the database (except sys schema) |
| DROP ANY MATERIALIZED VIEW       | Has the privilege to delete materialized views in any schema (except sys schema) |
| **Directories**:                 |                                                         |
| CREATE ANY DIRECTORY             | Has the privilege to create any directory object       |
| DROP ANY DIRECTORY               | Has the privilege to delete any directory object       |
| **Contexts**:                    |                                                         |
| CREATE ANY CONTEXT               | Has the privilege to create any context object         |
| DROP ANY CONTEXT                 | Has the privilege to delete any context object         |

## Other System Privileges

|Privilege Name |Description |
| --------------------------- | ---------------------------------- |
| **Store Outlines**:               |                                                         |
| CREATE ANY OUTLINE                | Has the privilege to create any store outline          |
| ALTER ANY OUTLINE                 | Has the privilege to modify attributes of any store outline |
| DROP ANY OUTLINE                  | Has the privilege to delete any store outline          |
| **Statistics**:                   |                                                         |
| ANALYZE ANY                       | Has the privilege to execute stored procedures to collect statistics related to non-sys schema using the DBMS_STATS package. With this privilege, statistics for tables and indexes under non-sys users can be collected. <br/> The privilege to collect statistics for sys schema and the entire database is only available to sys and DBA users. |
| **PROFILE**:                |                          |
| CREATE PROFILE                    | Has the privilege to create database resource limits    |
| ALTER PROFILE                     | Has the privilege to modify database resource limits    |
| DROP PROFILE                      | Has the privilege to delete database resource limits    |
| **Logical Logs**:                 |                                                         |
| YSTREAM_CAPTURE                   | * Has the privilege to execute procedures from the DBMS_YSTREAM_ADM package <br/> * Has the privilege to connect to the database via the YStream API to retrieve logical logs |
| **Import Privilege**:             |                                                         |
| FILE                              | Has the privilege to execute the LOAD DATA statement using the *yasql* tool for import |
| **Wallet Privilege**:             |                                                         |
| ADMINISTER KEY MANAGEMENT          | Has the privilege to execute statements for ADMINISTER KEY MANAGEMENT wallet |