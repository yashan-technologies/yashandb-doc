## General Description

ALTER SESSION is used to dynamically change the properties or session-level system configurations of the current session. These changes take effect only in the current session and will revert to the initial values once the session is terminated.

To execute this statement, the user must have the ALTER SESSION privilege.

## Statement Definition

**alter session::=**

```ebnf+diagram
syntax::= ALTER SESSION set_clause
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

After logging into the database, the default current Schema is the same as the logged-in user's Schema name. Users are allowed to manually switch the current schema by executing the ALTER SESSION statement. To confirm the current Schema of the session (i.e., the value of the current_schema parameter), it can be queried using [DBMS_UTILITY](../Built-in Packages/DBMS_UTILITY).OLD_CURRENT_SCHEMA().

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
