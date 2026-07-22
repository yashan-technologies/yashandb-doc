DBMS_METADATA包提供了一组内置程序接口，用于从数据库字典中检索元数据。

## GET_DDL

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

GET_DDL函数用于获取指定对象的元数据，该对象必须是一个可创建的对象。

本函数将返回与指定对象含义相同的创建语句（可能包含多条语句），但对于PL对象不返回'/'结束符，对于SQL对象不返回';'结束符。

|  参数| 描述|
| ----------- | ------------------------------------------------------------ |
| object_type | 要检索的对象的类型，不能为NULL，可输入的值有：<br />VIEW<br />FUNCTION<br />TRIGGER<br />PROCEDURE<br />PACKAGE<br />TYPE<br />TABLE：TAC表和EXTERNAL表不能获取创建语句<br />MATERIALIZED VIEW<br />INDEX |
| name        | 对象的名称，不能为NULL。使用双引号命名的对象按实际大小写，否则需使用全大写名称 |
| schema      | 对象所属用户。当此参数为NULL时，默认为当前登录用户           |
| version     | 提取元数据的版本。不能为NULL，选填，不生效                   |
| model       | 使用的模型。不能为NULL，选填，不生效                         |
| transform   | 转换的名称。不能为NULL，选填，不生效                         |

示例（HEAP表、LSC表）

```sql
-- 1. VIEW
CREATE VIEW v_area_meta AS SELECT * FROM area;

-- 查询v_area对象的元数据
SELECT DBMS_METADATA.GET_DDL('view', 'V_AREA_META') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE FORCE VIEW "SYS"."V_AREA_META" ("AREA_NO","AREA_NAME","DHQ") AS SELECT "AREA_NO","AREA_NAME","DHQ" FROM area

-- 按查询结果执行语句
CREATE OR REPLACE FORCE VIEW "SALES"."V_AREA"("AREA_NO","AREA_NAME","DHQ") AS SELECT "AREA_NO","AREA_NAME","DHQ" FROM area;
Succeed.

-- 2. PROCEDURE
CREATE OR REPLACE EDITIONABLE PROCEDURE P1_METADATA
AS 
c int;
BEGIN 
for i in 1..10 loop
 select 1 into c from dual;
 DBMS_OUTPUT.PUT_LINE ('c:'||c);
end loop;
END;
/

-- 查询P1_METADATA对象的元数据
SELECT DBMS_METADATA.GET_DDL('PROCEDURE','P1_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE PROCEDURE "SALES"."P1_METADATA"AS
c int;
BEGIN
for i in 1..10 loop
 select 1 into c from dual;
 DBMS_OUTPUT.PUT_LINE ('c:'||c);
end loop;
END;

-- 3. FUNCTION
create or replace function F1_METADATA(c int default 0) return int AS
begin
 return c;
 EXCEPTION
  WHEN OTHERS THEN
   DBMS_OUTPUT.PUT_LINE ('Unexpected error');
end;
/

-- 查询F1_METADATA对象的元数据
SELECT DBMS_METADATA.GET_DDL('FUNCTION','F1_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE FUNCTION "SALES"."F1_METADATA"(c int default 0) return int AS
begin
 return c;
 EXCEPTION
  WHEN OTHERS THEN
   DBMS_OUTPUT.PUT_LINE ('Unexpected error');
end;

-- 4. TRIGGER
CREATE table IF NOT EXISTS test_trigger (col1 INT);
create or replace trigger Tri_METADATA after insert on test_trigger
begin dbms_output.put_line('test follows table'); end;
/

-- 查询TRI_METADATA对象的元数据
SELECT DBMS_METADATA.GET_DDL('TRIGGER','TRI_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE TRIGGER "SALES"."TRI_METADATA"after insert on test_trigger
begin dbms_output.put_line('test follows table'); end;
ALTER TRIGGER "SALES"."TRI_METADATA" ENABLE

-- 5. PACKAGE
DROP PACKAGE IF EXISTS pkg_METADATA;
CREATE or replace PACKAGE pkg_METADATA AS
n1 varchar(200) := 'abc';
type rec_type is record(id int,name varchar(200) default 'unknown');
rec_var1 rec_type;
procedure p1_METADATA;
END pkg_METADATA;
/

CREATE OR REPLACE PACKAGE BODY pkg_METADATA IS
PROCEDURE P1_METADATA IS
BEGIN
	DBMS_OUTPUT.PUT_LINE ('c:');
END;
END pkg_METADATA;
/

-- 查询PKG_METADATA对象的元数据
SELECT DBMS_METADATA.GET_DDL('PACKAGE','PKG_METADATA') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 
CREATE OR REPLACE PACKAGE "SALES"."PKG_METADATA"AS
n1 varchar(200) := 'abc';
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
create table t1_metadata (col1 int, col2 float, col3 varchar(20), col4 number(20,6), col5 interval day(9) to second(6), col6 interval year(9) to month);

-- 查询T1_METADATA表的元数据
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
create global temporary table t1_metadata (col1 clob) on commit delete rows;

-- 查询T1_METADATA表的元数据
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
create table t1_metadata (col1 int)
partition by range(col1)
(partition p1 values less than(1),
partition p2 values less than(maxvalue));

-- 查询T1_METADATA表的元数据
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
PARTITION "P2" VALUES LESS THAN (maxvalue)
PCTFREE 8 INITRANS 2 MAXTRANS 255
TABLESPACE "USERS")
ORGANIZATION HEAP

-- LSC TABLE
DROP TABLE t1_metadata;
create table t1_metadata (col1 int, col2 int)
organization lsc
compression lz4 high
order by (col2, col1)
mcol ttl '1' month;

-- 查询T1_METADATA表的元数据
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

-- 创建MV1物化视图
CREATE TABLE MV1_metadata(col1 int, col2 int);
CREATE MATERIALIZED VIEW MV1 BUILD IMMEDIATE REFRESH COMPLETE ON DEMAND START WITH SYSDATE+1/24 NEXT SYSDATE+3/24 AS SELECT col1,col2 FROM MV1_metadata;

-- 查询MV1物化视图的元数据
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

-- 创建索引
CREATE TABLE index_table("a" int, b int);
CREATE INDEX T1_IDX on index_table("a"+b,"a" desc);

-- 查询索引的元数据
SELECT DBMS_METADATA.GET_DDL('INDEX', 'T1_IDX') FROM dual;
DBMS_METADATA.GET_DD                                             
---------------------------------------------------------------- 

CREATE INDEX "SYS"."T1_IDX" ON "SYS"."T1_IDX" ("a"+b, "a" DESC )
PCTFREE 8 INITRANS 2 MAXTRANS 255
TABLESPACE "SYSTEM"

1 row fetched.
```
