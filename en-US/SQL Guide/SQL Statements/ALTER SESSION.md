## General Description

ALTER SESSION is used to dynamically change the properties or session-level system configurations of the current session. These changes take effect only in the current session and will revert to the initial values once the session is terminated.

To execute this statement, the user must have the ALTER SESSION privilege.

## Statement Definition

**alter session::=**

```ebnf+diagram
syntax::= ALTER SESSION (set_clause|CLOSE DATABASE LINK dblink_name)
```

**set_clause::=**

```ebnf+diagram
syntax::= SET parameter_name "=" parameter_value
```

### 1. set\_clause

This statement is used to specify the values for session properties or session-level system configuration parameters.

#### 1.1. Session Property Parameter

**current_schema**

This parameter indicates the current Schema property of the session.

After logging into the database, the default current Schema is the same as the logged-in user's Schema name. Users are allowed to manually switch the current schema by executing the ALTER SESSION statement. To confirm the current Schema of the session (i.e., the value of the current_schema parameter), it can be queried using [DBMS_UTILITY](../../All Manuals/Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_UTILITY).OLD_CURRENT_SCHEMA().

When users perform SQL statement operations on schema objects, if they do not explicitly specify the Schema to which the object belongs, the system will default to operating on objects under the current_schema.

> **Note**: 
>
> The privilege check is based on the user, and switching the current schema does not affect the privileges of the currently logged-in user.

***Example***

```sql
-- There are two users in the system: sales1 and sales2
-- Log in as user sales1 (current schema is sales1)
conn sales1/1%2;

-- Create the area table (the area table belongs to sales1)
DROP TABLE IF EXISTS area;
CREATE TABLE area
(area_no CHAR(2) NOT NULL,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);
INSERT INTO area VALUES ('01','EastChina','Shanghai');
INSERT INTO area VALUES ('02','WestChina','Chengdu');
COMMIT;

SELECT area_no,area_name,DHQ FROM area;
AREA_NO AREA_NAME                  DHQ                   
------- -------------------------- -------------------
01      EastChina                       Shanghai             
02      WestChina                       Chengdu      

-- Change current_schema to sales2
ALTER SESSION SET current_schema=sales2;

-- Query the modification result
SELECT DBMS_UTILITY.OLD_CURRENT_SCHEMA() current_schema FROM dual;
CURRENT_SCHEMA
----------------------------------------------------------------
SALES2

-- Access the area table without explicitly specifying the schema; defaults to sales2.area, but the area table does not exist under sales2
SELECT area_no,area_name,DHQ FROM area;
[1:15]YAS-02012 table or view does not exist
```

#### 1.2. Session-Level System Configuration Parameters

The parameter_name can be specified as the session-level parameters from the [Configuration Parameters](../../All Manuals/Reference Manual/Configuration Parameters).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Session-level parameter date_format
SHOW PARAMETER date_format;
NAME                  VALUE   
--------------------- ------- 
DATE_FORMAT			  yyyy-mm-dd     
 
ALTER SESSION SET date_format='yyyy-mm-dd hh24:mi:ss';

SHOW PARAMETER date_format;
NAME                  VALUE   
--------------------- ------- 
DATE_FORMAT			  yyyy-mm-dd hh24:mi:ss 

-- Session-level parameter ISOLATION_LEVEL
-- Set the default isolation level for the session to SERIALIZABLE.
ALTER SESSION SET ISOLATION_LEVEL = SERIALIZABLE;

-- Set the default isolation level for the session to READ COMMITTED.
ALTER SESSION SET ISOLATION_LEVEL = READ COMMITTED;
```

<span id="closedblink" name="closedblink" class="yaslink"></span>

### 2. CLOSE DATABASE LINK

This statement is used to close DBLinks in the current session.


If a DBLink has been used in a session, the DBLink will remain open until the current session ends or the DBLink is manually closed. DBLink may allocate system free memory to cache remote table business data. If it is confirmed that a DBLink is temporarily not needed again, this statement can be used to explicitly close the target DBLink and release the corresponding resources.


dblink_name must be specified as the name of an already opened remote database link, and it must be ensured that transactions related to this link have been committed or rolled back. The currently opened DBLinks and their transaction status in the current session can be obtained through the [V$DBLINK](../../All Manuals/参考手册/系统视图/动态视图/V$DBLINK) view.


***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Query DBLinks that are currently open in the session and whether they are in a transaction
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK;

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
LinkToOra                                                        YES

-- IN_TRANSACTION=YES indicates that it is currently in a transaction. The transaction needs to be committed or rolled back as needed before the DBLink can be closed
COMMIT;

-- Confirm the transaction status of the target DBLink
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
LinkToOra                                                        NO

-- Close the target DBLink
ALTER SESSION CLOSE DATABASE LINK LinkToOra;

-- Confirm the closure result
SELECT DB_LINK,IN_TRANSACTION FROM V$DBLINK WHERE DB_LINK = 'LinkToOra';

DB_LINK                                                          IN_TRANSACTION
---------------------------------------------------------------- --------------
```
