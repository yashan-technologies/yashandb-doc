
**dblink::=**

```ebnf+diagram
syntax::= "@" dblink_name
```

该语法用于YashanDB（源端）对远端数据库（目的端）的表操作，目的端可以为同构数据库（YashanDB）或异构数据库（Oracle）。

dblink_name指在[CREATE DATABASE LINK](../../SQL语句/CREATE DATABASE LINK)时所创建的远端数据库名称。

## 使用说明

使用dblink功能时，崖山数据库会启动[沙箱进程](yex_server沙箱进程管理)进行相关数据操作。

- 通过dblink操作远端数据库的表时，需满足如下要求：
  >**Note**:
  >
  > 在存算一体分布式集群部署中，操作远端表时采用列存，除下表所列数据类型要求外，还应**遵循列存的[数据类型](../../数据类型/00数据类型)支持范围**。

  |  目的端为Oracle| 目的端为YashanDB|
  |--------------------|--------------------------------------|
  |远端表字段需为下列数据类型：<br/>SMALLINT<br/>INT<br/>FLOAT/BINARY_FLOAT<br/>BINARY_DOUBLE<br/>NUMBER/DECIMAL<br/>DATE<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>NCHAR<br/>VARCHAR2<br/>NVARCHAR2<br/>RAW<br/>BLOB（源端为存算一体分布式集群部署时，不适用）<br/>CLOB（源端为存算一体分布式集群部署时，不适用）<br/>NCLOB（源端为存算一体分布式集群部署时，不适用）  |远端表字段需为下列数据类型：<br/>TINYINT<br/>SMALLINT<br/>INT<br/>BIGINT<br/>FLOAT/BINARY_FLOAT<br/>DOUBLE/BINARY_DOUBLE<br/>NUMBER<br/>BIT<br/>BOOLEAN<br/>DATE<br/>TIME<br/>TIMESTAMP<br/>INTERVAL YEAR TO MONTH<br/>INTERVAL DAY TO SECOND<br/>CHAR<br/>VARCHAR<br/>RAW<br/>ROWID（源端为存算一体分布式集群部署时，不适用）  |

- 在存算一体分布式集群部署中，无法对远端数据库的表进行INSERT、UPDATE、DELETE、SEQUENCE、PROCEDURE以及FUNCTION操作。

- 在使用dblink的场景下，不支持通过[二阶段提交（Two-phase Commit）](../../../../概念手册/附：术语表.html#2para)保证所有资源同时提交或回滚某个事务。

- 对dblink远端数据库进行事务操作时，只支持READ COMMITTED事务隔离级别。

- 暂未适配临时表，如若对远端临时表进行操作，结果可能与预期不符。

## 配置沙箱进程运行参数

### DBLINK\_ROWARRAY\_SIZE

获取一批远端数据的最大行数，执行时将根据数据大小进行动态调整。默认值为32K，取值范围为[1K,1T]。

若调整该值小于最小值1K不会报错，但实际生效仍采用最小值。

### DRV\_MEMORY\_BLOCK\_SIZE

内存页面大小，YDBC_BUFFER将自动根据该参数值进行切分。默认值为512K，取值范围为[64K,1T]。

若调整该值小于最小值64K不会报错，但实际生效仍采用最小值。

### EXS\_MAX\_XACTS

同时持有dblink链接组的最大个数，一个session最多一个链接组，默认值为1024，取值范围为[1024,16384]。

若调整该值小于最小值1024不会报错，但实际生效仍采用最小值。

### MAX\_BEATING\_FAILS

检测yex_server进程心跳失败的次数阈值，累积失败次数超过该值则视为yasdb与yex_server通信中断，将重新拉起yex_server进程。默认值为3，取值范围为[3,255]。

若调整该值小于最小值3不会报错，但实际生效仍采用最小值。

### MAX\_DBLINK\_CONNS

并发场景下，不同用户session访问同一个dblink，在同一个时间窗口使用的conn数量上限。默认值为64，取值范围为[64,16384]。

若调整该值小于最小值64不会报错，但实际生效仍采用最小值。

## 查询远端表

对远端表进行[SELECT](../../SQL语句/SELECT)操作时，存在如下约束：

- 不能采用table.column@dblink形式。
- 单次默认最多查询32个远端表，可通过系统参数`dblink_cursor_count`调整规格。
- 无法查询远端表的隐藏列。

示例

```sql
-- 创建远端表
conn sys/********
CREATE TABLE table_test(c1 INT);

-- 创建dblink，并访问远端表
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';
SELECT * FROM table_test@link_test;

-- 更改远端表的元数据
conn sys/********
ALTER TABLE table_test ADD COLUMN(c2 INT);

conn sales/sales

SELECT * FROM table_test@link_test;
C1           C2          
------------ ------------

```

## 插入远端表

对远端表进行[INSERT](../../SQL语句/INSERT)操作时，存在如下约束：

- 不允许多表INSERT。
- 不允许指定分区INSERT。
- 不允许执行INSERT DUPLICATE UPDATE语句。
- 不允许执行INSERT RETURN语句。
- INSERT SELECT不支持LOB类型。

示例（单机、共享集群部署）

```sql
-- 创建远端表
conn sys/********
CREATE TABLE table_test(c1 INT, c2 INT);

-- 创建dblink，并访问远端表
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';
SELECT * FROM table_test@link_test;

-- 插入数据
INSERT INTO table_test@link_test VALUES(1,2);
```

## 更新远端表

对远端表进行[UPDATE](../../SQL语句/UPDATE)操作时，存在如下约束：

- filter与更新本地数据库的对象的filter相比：
  - 不能使用聚集函数。
  - 不能使用窗口函数。
  - 不能使用子查询。
  - 不能使用序列。
  - 不能使用自定义函数（包括UPDATE SET语句）。
- 不允许多表UPDATE。
- 不允许指定分区UPDATE。

示例（单机、共享集群部署）

```sql
-- 创建远端表
conn sys/********
CREATE TABLE table_test(c1 INT, c2 INT);

-- 创建dblink
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';

-- 更改远端表的元数据
UPDATE table_test@link_test SET c1=1,c2=2;
```

## 删除远端表

对远端表进行[DELETE](../../SQL语句/DELETE)操作时，存在如下约束：

- filter与删除本地数据库的对象的filter相比：
  - 不能使用聚集函数。
  - 不能使用窗口函数。
  - 不能使用子查询。
  - 不能使用序列。
  - 不能使用自定义函数。
- 不允许多表DELETE。
- 不允许指定分区DELETE。

示例（单机、共享集群部署）

```sql
conn sys/********
CREATE TABLE table_test(c1 INT);

-- 创建dblink
conn sales/sales
CREATE DATABASE link link_test CONNECT TO sys IDENTIFIED BY sys USING '192.168.1.2:1688';

-- 删除远端表数据
DELETE FROM table_test@link_test WHERE c1=1;
```

## 调用远端子程序

**callDblinkProc::=**

```ebnf+diagram
syntax::= func_name@dblink_name(arg1, arg2, ...)
```

调用远端子程序时，存在如下约束：

- 远端数据库必须为Oracle数据库。

- 远端子程序对象只能为存储过程、函数或用户自定义包。

- 远端子程序的参数类型必须为标量数据类型。

- 若远端子程序为重载子程序，还要求：

    - 参数个数不能相同。

    - 调用时，必须补全目标子程序中所有参数的实际参数值。

示例（单机、共享集群部署）

```sql
-- 在Oracle数据库中创建子程序

conn oradb/oradb
CREATE FUNCTION func_test(c INT) RETURN INT IS
BEGIN
    RETURN 100;
END;
/

CREATE PROCEDURE proc_test(c INT) IS
BEGIN
    dbms_output.put_line(c);
END;
/

CREATE OR replace PACKAGE pkg_test AS
    FUNCTION  func_test(c NUMBER) RETURN INT;
    procedure proc_test(c number);
end pkg_test;
/
CREATE OR replace PACKAGE body pkg_test AS
    FUNCTION func_test(c NUMBER) RETURN INT IS
    BEGIN
        RETURN c + 100;
    END;
    PROCEDURE proc_test(c NUMBER) IS
    BEGIN
       dbms_output.put_line(c);
    END;
END pkg_test;
/

-- 创建dblink
conn sales/sales
CREATE DATABASE link link_test CONNECT TO oradb IDENTIFIED BY oradb USING 'oracle:192.168.1.3:1521/orainst';

-- 调用远端子程序
SELECT func_test@link_test(10) FROM dual;
SELECT pkg_test.func_test@link_test(10) FROM dual;
BEGIN
   proc_test@link_test(100);
   pkg_test.proc_test@link_test(100);
END;
/
```

