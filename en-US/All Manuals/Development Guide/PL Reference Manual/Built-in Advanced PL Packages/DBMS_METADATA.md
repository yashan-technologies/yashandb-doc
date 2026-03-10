The DBMS_METADATA package provides a set of built-in program interfaces for retrieving metadata from the database dictionary.

## GET\_DDL

```plsql
DBMS_METADATA.GET_DDL (
object_type     IN VARCHAR,
name            IN VARCHAR,
schema          IN VARCHAR DEFAULT NULL,
version         IN VARCHAR,
model           IN VARCHAR,
transform       IN VARCHAR)
RETURN CLOB;
```

The GET_DDL function is used to obtain metadata for a specified object, which must be a creatable object.

This function returns a CREATE statement that is equivalent in meaning to the specified object (which may include multiple statements), but does not return the '/' terminator for PL objects and does not return the ';' terminator for SQL objects.

|Parameter |Description |
| ----------- | ------------------------------------------------------------ |
| object_type  | The type of the object to be retrieved, cannot be NULL. Valid values include:<br />VIEW<br />FUNCTION<br />TRIGGER<br />PROCEDURE<br />PACKAGE<br />TYPE<br />TABLE: TAC tables and EXTERNAL tables cannot retrieve creation statements<br />MATERIALIZED VIEW<br />INDEX |
| name         | The name of the object, cannot be NULL. Objects named with double quotes are case-sensitive; otherwise, the name must be in all uppercase. |
| schema       | The user to whom the object belongs. If this parameter is NULL, it defaults to the currently logged-in user. |
| version      | The version of the metadata to be retrieved. Cannot be NULL, optional, and ineffective. |
| model        | The model used. Cannot be NULL, optional, and ineffective. |
| transform    | The name of the transformation. Cannot be NULL, optional, and ineffective. |

***Example*** for Heap tables and LSC tables

```sql
-- 1. VIEW
CREATE VIEW v_area_meta AS SELECT * FROM area;

-- Query metadata for the v_area object
SELECT DBMS_METADATA.GET_DDL('view', 'V_AREA_META') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE FORCE VIEW "SYS"."V_AREA_META" ("AREA_NO","AREA_NAME","DHQ") AS SELECT "AREA_NO","AREA_NAME","DHQ" FROM area

-- EXECUTE the statement based ON the query result
CREATE OR REPLACE FORCE VIEW "SALES"."V_AREA"("AREA_NO","AREA_NAME","DHQ") AS SELECT "AREA_NO","AREA_NAME","DHQ" FROM area;

-- 2. PROCEDURE
CREATE OR REPLACE EDITIONABLE PROCEDURE P1_METADATA
AS 
c INT;
BEGIN 
FOR i IN 1..10 LOOP
 SELECT 1 INTO c FROM dual;
 DBMS_OUTPUT.PUT_LINE ('c:'||c);
END LOOP;
END;
/

-- Query metadata for the P1_METADATA object
SELECT DBMS_METADATA.GET_DDL('PROCEDURE','P1_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE PROCEDURE "SALES"."P1_METADATA"AS
c INT;
BEGIN
FOR i IN 1..10 LOOP
 SELECT 1 INTO c FROM dual;
 DBMS_OUTPUT.PUT_LINE ('c:'||c);
END LOOP;
END;

-- 3. FUNCTION
CREATE OR replace FUNCTION F1_METADATA(c INT DEFAULT 0) RETURN INT AS
BEGIN
 RETURN c;
 EXCEPTION
  WHEN OTHERS THEN
   DBMS_OUTPUT.PUT_LINE ('Unexpected error');
END;
/

-- Query metadata for the F1_METADATA object
SELECT DBMS_METADATA.GET_DDL('FUNCTION','F1_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE FUNCTION "SALES"."F1_METADATA"(c INT DEFAULT 0) RETURN INT AS
BEGIN
 RETURN c;
 EXCEPTION
  WHEN OTHERS THEN
   DBMS_OUTPUT.PUT_LINE ('Unexpected error');
END;

-- 4. TRIGGER
CREATE TABLE IF NOT EXISTS test_trigger (col1 INT);
CREATE OR replace TRIGGER Tri_METADATA after INSERT ON test_trigger
BEGIN dbms_output.put_line('test follows table'); END;
/

-- Query metadata for the TRI_METADATA object
SELECT DBMS_METADATA.GET_DDL('TRIGGER','TRI_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE TRIGGER "SALES"."TRI_METADATA"after INSERT ON test_trigger
BEGIN dbms_output.put_line('test follows table'); END;
ALTER TRIGGER "SALES"."TRI_METADATA" ENABLE

-- 5. PACKAGE
DROP PACKAGE IF EXISTS pkg_METADATA;
CREATE OR replace PACKAGE pkg_METADATA AS
n1 VARCHAR(200) := 'abc';
TYPE rec_type IS RECORD(id INT,name VARCHAR(200) DEFAULT 'unknown');
rec_var1 rec_type;
PROCEDURE p1_METADATA;
END pkg_METADATA;
/

CREATE OR REPLACE PACKAGE BODY pkg_METADATA IS
PROCEDURE P1_METADATA IS
BEGIN
	DBMS_OUTPUT.PUT_LINE ('c:');
END;
END pkg_METADATA;
/

-- Query metadata for the PKG_METADATA object
SELECT DBMS_METADATA.GET_DDL('PACKAGE','PKG_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE PACKAGE "SALES"."PKG_METADATA"AS
n1 VARCHAR(200) := 'abc';
type rec_type is record(id int,name varchar(200) default 'unknown');
rec_var1 rec_type;
procedure p1_METADATA;
END pkg_METADATA;
CREATE OR REPLACE PACKAGE BODY "SALES"."PKG_METADATA"IS
PROCEDURE P1_METADATA IS
BEGIN
	DBMS_OUTPUT.PUT_LINE ('c:');
END;
END pkg_METADATA;

-- 6. TABLE

-- NORMAL TABLE
DROP TABLE IF EXISTS t1_metadata;
CREATE TABLE t1_metadata (col1 INT, col2 FLOAT, col3 VARCHAR(20), col4 NUMBER(20,6), col5 INTERVAL DAY(9) TO SECOND(6), col6 INTERVAL YEAR(9) TO month);

-- Query metadata FOR the T1_METADATA TABLE
SELECT DBMS_METADATA.GET_DDL('TABLE', 'T1_METADATA') FROM dual;
DBMS_METADATA.GET_DD
----------------------------------------------------------------
CREATE TABLE "SALES"."T1_METADATA"
("COL1" INTEGER,
"COL2" FLOAT,
"COL3" VARCHAR(20),
"COL4" NUMBER(20, 6),
"COL5" INTERVAL DAY(9) TO SECOND(6),
"COL6" INTERVAL YEAR(9) TO MONTH
) PCTFREE 8 INITRANS 2 MAXTRANS 255
LOGGING
TABLESPACE "USERS"
SEGMENT CREATION DEFERRED
ORGANIZATION HEAP

-- TEMPORARY TABLE
DROP TABLE t1_metadata;
CREATE GLOBAL TEMPORARY TABLE t1_metadata (col1 CLOB) ON COMMIT DELETE ROWS;

-- Query metadata FOR the T1_METADATA TABLE
SELECT DBMS_METADATA.GET_DDL('TABLE', 'T1_METADATA') FROM dual;
DBMS_METADATA.GET_DD
----------------------------------------------------------------
CREATE GLOBAL TEMPORARY TABLE "SALES"."T1_METADATA"
("COL1" CLOB
) ON COMMIT DELETE ROWS
LOB ("COL1") STORE AS (
TABLESPACE "TEMP" ENABLE STORAGE IN ROW)
ORGANIZATION HEAP

-- PARTITION TABLE
DROP TABLE t1_metadata;
CREATE TABLE t1_metadata (col1 INT)
PARTITION BY RANGE(col1)
(PARTITION p1 VALUES LESS than(1),
PARTITION p2 VALUES LESS than(MAXVALUE));

-- Query metadata FOR the T1_METADATA TABLE
SELECT DBMS_METADATA.GET_DDL('TABLE', 'T1_METADATA') FROM dual;
DBMS_METADATA.GET_DD
----------------------------------------------------------------
CREATE TABLE "SALES"."T1_METADATA"
("COL1" INTEGER
) PCTFREE 8 INITRANS 2 MAXTRANS 255
LOGGING
TABLESPACE "USERS"
SEGMENT CREATION DEFERRED
PARTITION BY RANGE ("COL1")
(PARTITION "P1" VALUES LESS THAN (1)
PCTFREE 8 INITRANS 2 MAXTRANS 255
TABLESPACE "USERS",
PARTITION "P2" VALUES LESS THAN (MAXVALUE)
PCTFREE 8 INITRANS 2 MAXTRANS 255
TABLESPACE "USERS")
ORGANIZATION HEAP

-- LSC TABLE
DROP TABLE t1_metadata;
CREATE TABLE t1_metadata (col1 INT, col2 INT)
ORGANIZATION LSC
COMPRESSION lz4 high
ORDER BY (col2, col1)
MCOL TTL '1' month;

-- Query metadata FOR the T1_METADATA TABLE
SELECT DBMS_METADATA.GET_DDL('TABLE', 'T1_METADATA') FROM dual;
DBMS_METADATA.GET_DD
----------------------------------------------------------------
CREATE TABLE "SALES"."T1_METADATA"
("COL1" INTEGER COMPRESSION LZ4 HIGH ENCODING PLAIN,
"COL2" INTEGER COMPRESSION LZ4 HIGH ENCODING PLAIN
) PCTFREE 8 INITRANS 2 MAXTRANS 255
LOGGING
TABLESPACE "USERS"
SEGMENT CREATION DEFERRED
COMPRESSION LZ4 HIGH
ORDER BY ("COL2","COL1") ASC NULLS FIRST
MCOL TTL '2678400' SECOND
ORGANIZATION LSC
ENABLE ROW MOVEMENT

-- 7. MATERIALIZED VIEW

-- CREATE the MV1 materialized VIEW
CREATE TABLE MV1_metadata(col1 INT, col2 INT);
CREATE MATERIALIZED VIEW MV1 BUILD IMMEDIATE REFRESH COMPLETE ON DEMAND START WITH SYSDATE+1/24 NEXT SYSDATE+3/24 AS SELECT col1,col2 FROM MV1_metadata;

-- Query metadata FOR the MV1 materialized VIEW
SELECT DBMS_METADATA.GET_DDL('MATERIALIZED VIEW', 'MV1') FROM dual;
DBMS_METADATA.GET_DD
----------------------------------------------------------------
CREATE MATERIALIZED VIEW "SALES"."MV1" ("COL1","COL2")
TABLESPACE SYSTEM
BUILD IMMEDIATE
REFRESH COMPLETE ON DEMAND START WITH SYSDATE+1/24 NEXT SYSDATE+3/24
DISABLE QUERY REWRITE
AS SELECT "COL1","COL2" FROM MV1_metadata

-- 8. INDEX

-- CREATE INDEX
CREATE TABLE index_table("a" INT, b INT);
CREATE INDEX T1_IDX ON index_table("a"+b,"a" DESC);

-- Query metadata FOR the INDEX
SELECT DBMS_METADATA.GET_DDL('INDEX', 'T1_IDX') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 

CREATE INDEX "SYS"."T1_IDX" ON "SYS"."T1_IDX" ("a"+b, "a" DESC )
PCTFREE 8 INITRANS 2 MAXTRANS 255
TABLESPACE "SYSTEM"

```
