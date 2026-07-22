本章节将列举ODBC驱动对Oracle DBLINK的支持情况。

## 已支持功能

### 通过Oracle高级包传递命令

Oracle数据库可使用DBMS_HS_PASSTHROUGH高级包传递命令至YashanDB数据库，该高级包支持传递`ALTER`、`CREATE`、`DROP`和`GRANT`语句，高级包具体描述及限制请参考Oracle官方文档。

该高级包不支持传递如下命令：

- `BEGIN TRANSACTION`
- `COMMIT`
- `ROLLBACK`
- `SAVE`
- `SHUTDOWN`

示例

```plsql
DECLARE
  num_rows INTEGER;
BEGIN
  num_rows := DBMS_HS_PASSTHROUGH.EXECUTE_IMMEDIATE@YASDBODBC('insert into t1 values (1),(2)');
END;
/
--成功

DECLARE
  num_rows INTEGER;
BEGIN
  num_rows := DBMS_HS_PASSTHROUGH.EXECUTE_IMMEDIATE@YASDBODBC('commit');
END;
/
--不支持传递COMMIT命令，返回错误
```

### COPY命令

该命令可用于不同数据库中表数据的复制。此特性为SQLPlus特性，仅支持在SQLPlus中使用。

语法：

```ebnf
= COPY {FROM database | TO database | FROM database TO database}{APPEND|CREATE|INSERT|REPLACE} destination_table [(column, column, column, ...)] USING query.
```

**database::=**

```ebnf
= username[/password]@connect_identifier.
```

支持的数据类型：

- CHAR
- DATE
- LONG
- NUMBER
- VARCHAR2

支持APPEND|CREATE|INSERT|REPLACE四种用法，用法的具体描述请参考SQLPlus官方文档。如执行语句导致YashanDB数据库中会创建新表，则返回错误。

示例

```sql
--示例中oracle@ORCL为连接Oracle数据库中的oracle用户，YASDBODBC为配置的YashanDB DBLINK
--前置建表操作
YashanDB:
create table copy_table (col1 int);
Oracle:
create table copy_table (col1 int);

--INSERT
copy from oracle@ORCL insert copy_table@YASDBODBC using select * from copy_table;
copy to oracle@ORCL insert copy_table using select * from copy_table@YASDBODBC;

--CREATE
copy to oracle@ORCL create copy_tablenew using select * from copy_table@YASDBODBC;
--由于YashanDB中不存在表copy_tablenew，执行如下语句将在YashanDB数据库中创建该表，返回错误
copy from oracle@ORCL create copy_tablenew@YASDBODBC using select * from copy_table;
ORA-02021: DDL operations are not allowed on a remote database

--APPEND
copy from oracle@ORCL append copy_table@YASDBODBC using select * from copy_table;
copy to oracle@ORCL append copy_table using select * from copy_table@YASDBODBC;

--REPLACE
copy to oracle@ORCL replace copy_table using select * from copy_table@YASDBODBC;
--由于YashanDB中已存在表copy_table，如下语句会删除该表并创建包含复制数据的新表，返回错误
copy from oracle@ORCL replace copy_table@YASDBODBC using select * from copy_table;
ORA-02021: DDL operations are not allowed on a remote database
```

### 增删改查能力

支持Oracle数据库中通过在`DELETE`、`INSERT`、`SELECT`和`UPDATE`语句中指定DBLINK的方式，远程连接至YashanDB数据库中执行语句，语句的具体描述限制请参考Oracle官方文档。

示例

```sql
--建表语句
YashanDB: 
create table example_table (col1 int);
INSERT INTO example_table VALUES (4);
INSERT INTO expmple_table VALUES (6);

--SELECT
SELECT * FROM example_table@YASDBODBC;

--INSERT (不支持多组values)
INSERT INTO example_table@YASDBODBC VALUES (2);

--UPDATE
UPDATE example_table@YASDBODBC SET col1=8 WHERE col1=6;

--DELETE
DELETE FROM example_table@YASDBODBC;
```

### 绑定参数支持

支持ODBC的出入参绑定。

## 未支持功能

- 数据字典
