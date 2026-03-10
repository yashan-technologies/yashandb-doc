## 通用描述

LOCK TABLE用于对表加读写锁，执行该操作的用户需对操作对象具备读写权限。

## 语句定义

**lock table::=**

```ebnf+diagram
syntax::= LOCK TABLE ([ schema"." ] table_name (READ | WRITE)){","([ schema"." ] table_name (READ | WRITE))} ";"
```

**unlock table::=**

```ebnf+diagram
syntax::= UNLOCK TABLE ";"
```

LOCK TABLES为LOCK TABLE同义词，用法及含义相同。

- 执行LOCK TABLE后，当前会话只能对锁表执行读写，无法读写其他表。

- 执行LOCK TABLE后，其他会话只能对锁表进行查询，无法对表及表数据进行操作。

- 执行LOCK TABLE前会释放所有已通过LOCK TABLE获取的锁。

- LOCK TABLE语句生效时，将禁止执行DDL语句，包含CREATE TABLE、CREATE TABLE AS、CREATE VIEW、DROP VIEW以及关于存储过程等DDL语句。

- LOCK TABLE语句无法在存储过程、匿名块中执行。

- 锁类型READ和WRITE当前版本中表现一致，可以同时读写。

UNLOCK TABLE用于释放当前会话获取的所有表锁。UNLOCK TABLES为UNLOCK TABLE同义词，用法及含义相同。

示例

```sql
-- 在会话一创建数据库、创建表并锁定表
CREATE DATABASE db_lk;

USE db_lk;

CREATE TABLE area
(area_no CHAR(2) NOT NULL PRIMARY KEY,
 area_name VARCHAR2(60),
 DHQ VARCHAR2(20) DEFAULT 'ShenZhen' NOT NULL);

INSERT INTO area VALUES ('01','EastChina','Shanghai');

LOCK TABLE area WRITE;

-- 在会话二中对锁定表进行操作，发现系统一直处于查询等待状态
INSERT INTO area VALUES ('18','CentralChina','Chongqing');

-- 在会话三中查询PROCESSLIST视图中对应的状态，可以看到该会话正在等待系统锁
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
