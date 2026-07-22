## General Description

The LOCK TABLE statement is used to apply table locks for READ or WRITE. The user executing this operation must have the privileges on the objects.

## Statement Definition

**lock table::=**

```ebnf
= LOCK TABLE ([ schema'.' ] table_name (READ | WRITE)){','([ schema'.' ] table_name (READ | WRITE))} ';'.
```

**unlock table::=**

```ebnf
= UNLOCK TABLE ';'.
```

LOCK TABLES is a synonym for LOCK TABLE, with identical usage and meaning.

- After executing LOCK TABLE, the current session can only read and write to the locked table, and cannot read or write to other tables.

- After executing LOCK TABLE, other sessions can only query the locked table, and cannot perform operations on the table or table data.

- Before executing LOCK TABLE, all locks acquired through LOCK TABLE will be released.

- When the LOCK TABLE statement is in effect, DDL statements will be prohibited, including CREATE TABLE, CREATE TABLE AS, CREATE VIEW, DROP VIEW, and DDL statements related to stored procedures.

- The LOCK TABLE statement cannot be executed in stored procedures or anonymous blocks.

- The lock types READ and WRITE behave consistently in the current version, allowing simultaneous reading and writing.

UNLOCK TABLE is used to release all table locks acquired by the current session. UNLOCK TABLES is a synonym for UNLOCK TABLE, with identical usage and meaning.

***Example*** 

```sql
-- Create database and table, and then lock the table in Session ONE.
CREATE DATABASE db_lk;

USE db_lk;

CREATE TABLE area
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

INSERT INTO area VALUES ('01','EastChina','Shanghai');

LOCK TABLE area WRITE;

-- Performing operations on the locked table in session two, the system remains in a query waiting state
INSERT INTO area VALUES ('18','CentralChina','Chongqing');

-- Querying PROCESSLIST view fro the corresponding status in Session THREE, you can see that the statement is waiting for the system lock to be released.
SELECT * FROM INFORMATION_SCHEMA.PROCESSLIST;
+------+------+--------------------+-------+---------+------+-------------+------------------------------------------------------+
| ID   | USER | HOST               | DB    | COMMAND | TIME | STATE       | INFO                                                 |
+------+------+--------------------+-------+---------+------+-------------+------------------------------------------------------+
|   42 | wzq1 | 172.16.60.92:3707  | DB_LK | Query   |  144 | System lock | INSERT INTO area VALUES ('02','WestChina','Chengdu') |
|   44 | wzq1 | 172.16.60.92:4731  | DB_LK | Query   |    0 |             | SELECT * FROM INFORMATION_SCHEMA.PROCESSLIST         |
|   40 | wzq1 | 172.16.60.92:28790 | DB_LK | Sleep   |  170 |             | NULL                                                 |
|   41 | SYS  | NULL               | SYS   | Sleep   |  852 |             | NULL                                                 |
+------+------+--------------------+-------+---------+------+-------------+------------------------------------------------------+
4 rows in set (0.02 sec)

```
