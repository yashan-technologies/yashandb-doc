查看帮助和版本信息
----

使用imp -h或imp --help命令可显示帮助和版本信息并退出。

```shell
$ imp -h

# 或者：

$ imp --help
```

使用imp -v命令可显示版本信息并退出。

```shell
$ imp -v
```

## 命令格式

```shell
imp username/password@ip:port [KEYWORD[=value1,value2,...,valueN]]
```

**username/password**

执行导入命令的数据库用户（登录用户）、密码、地址（默认为本机），对其权限要求见不同导入模式具体描述。

**KEYWORD**

命令选项：

- FILE：导入文件的名称，必须输入。
- 导入模式：
  - FULL：整个文件导入，Value为单值Y或N。
  - FROMUSER：用户导入模式命令，Value为用户名称。
    - TOUSER：更换导入的用户，Value为用户名称，须在FROMUSER模式下指定，可省略，省略则默认不更换导入的用户。
  - TABLES：表导入模式命令，Value为表名称。
  - 须选择如上一种模式进行导入，如命令中未指定导入模式，则默认为FROMUSER。
- IGNORE: 忽略创建对象时的错误，Value为单值Y或N，可省略，省略则默认为N。
- ROWS：用于指定是否导入表数据，Value为单值Y或N，可省略，省略则默认为Y。
- DATA_ONLY：用于指定是否仅导入表数据，Value为单值Y或N，可省略，省略则默认为N。
- LOG_PATH：用于指定导入日志的路径，Value为路径名，可省略，省略则不生成日志文件。
- LOG_LEVEL：用于指定导入日志的日志级别，Value为[ERROR, WARN, INFO, DEBUG, TRACE]，可省略，省略则默认为INFO。
- TRUNCATE：用于指定导入文件中待导入的表已在目标数据库中存在时是否直接覆盖，Value为单值Y或N，可省略，省略则默认为N。
- MOJIBAKE_REPLACE：用于指定是否对数据文件中无法被源数据字符集识别的乱码内容进行容错。Value为单值Y或N，可省略，省略则默认为N，表示不对乱码进行容错，若源字节流中存在工具设定字符集不支持的乱码则可能导入失败。<br />乱码容错机制如下：<br />1. 当源字节流中因乱码导致字符集转换失败时，跳过源字节流的1个字节，并在目标缓冲区增加一个`?`字符。<br />2. 若还存在乱码，重复执行上一步直至源字节流结尾。 

示例

```shell
$ imp sales/sales@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales
```

## 命令选项

exp从源端YashanDB导出的元数据文件中包含了部署形态以及表类型default_table_type相关信息，要求通过imp导入元数据文件时，目标端YashanDB的default_table_type参数必须与元数据文件中信息一致，可通过数据库客户端执行`SHOW parameter DEFAULT_TABLE_TYPE;`命令查看源端和目标端数据库系统参数配置是否一致。

### FULL模式

FULL模式用于导入文件中的所有数据，具体数据以文件为准。导入可支持以下对象：

- 用户元数据
- 用户下的所有对象元数据
- 用户下的表数据
- 所有的系统权限
- 所有的对象权限
- 所有的角色
- 所有的审计策略/使能
- 所有的OUTLINE
- 所有的PROFILE
- 所有的定时任务
- 所有的LBAC相关信息

在YashanDB，PROFILE、LBAC相关信息仅支持在FULL模式下导入。


执行本模式导入的数据库用户必须已创建且拥有DBA角色权限。Value值的含义为：

- Y：导入整个文件的数据，此时要求必须以sys作为导入操作用户。
- N：导入登录用户下的数据。

在执行FULL模式导入之前，要求目标端YashanDB上已创建同名表空间；导入时若导入对象已经存在，则跳过该导入对象继续执行后续导入任务。

在执行本模式导入之前，要求打开LBAC开关，否则LBAC相关信息将导入失败，LBAC开关操作参考[YLS_ENFORCEMENT](../../开发手册/PL参考手册/内置高级包/YLS_ENFORCEMENT.md)。


示例

```sql
-- 使用YashanDB提供的命令或工具在目标数据库上创建同名表空间（本示例使用默认USERS表空间）

-- 执行整库导入
$ imp sys/********@127.0.0.1:1688 FILE=export.full.dump FULL=Y

-- 统计导入之后对象信息
SELECT COUNT(1) ts_count FROM dba_tablespaces;
             TS_COUNT 
--------------------- 
                    6
SELECT COUNT(1) user_count FROM dba_users;
           USER_COUNT 
--------------------- 
                    3
SELECT COUNT(1) table_count FROM dba_tables;
           TABLE_COUNT 
--------------------- 
                   89
SELECT COUNT(1) index_count FROM dba_indexes;
           INDEX_COUNT 
--------------------- 
                  134
-- 统计SALES用户下的area表数据
SELECT COUNT(1) sales_data FROM sales.area;
           SALES_DATA 
--------------------- 
                    5
-- 统计SALES0用户下的area表数据
SELECT COUNT(1) sales0_data FROM sales0.area;
          SALES0_DATA 
--------------------- 
                    5
```

### FROMUSER模式

FROMUSER模式用于导入指定用户下的对象及数据，包括：

- 用户下的所有对象元数据
- 用户下的表数据
- 用户下对象的对象权限

本模式命令的Value值为用户名称，可以为多个，输入重复用户名时将只导入一次。若指定的用户不在数据文件中，则提示Warning。

对于执行本模式导入的数据库用户，如Value值与此用户相同，即导入本用户下的数据，则对其权限无要求；如Value值中存在非本用户，即导入其他用户下的数据，则要求其必须拥有DBA角色权限。

在执行本模式导入之前，要求目标端YashanDB上已创建同名表空间；若导入对象已经存在，则导入失败。

如果存在跨用户的依赖关系，且依赖对象在数据库里不存在，将无法导入成功。

示例

```sql
-- 使用YashanDB提供的命令或工具在目标数据库上新建同名表空间（本示例使用默认USERS表空间）

-- 创建拥有DBA权限的用户sales0，和普通用户sales
CREATE USER sales0 IDENTIFIED BY sales0;
GRANT DBA TO sales0;
CREATE USER sales IDENTIFIED BY sales;
GRANT CREATE SESSION TO sales;

-- 导入sales用户数据
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales

-- 以sales用户登录，统计相关数据
SELECT COUNT(1) table_count FROM user_objects;
             COUNT(1) 
--------------------- 
                   30
SELECT COUNT(1) data_count FROM sales_info;
           DATA_COUNT 
--------------------- 
                    6
```

### TOUSER

TOUSER命令用于更换导入的用户，将导入对象的OWNER更换成TOUSER指定的用户。 

本命令的Value值为用户名称，可以为多个，输入重复用户名时会多次导入，默认值为NONE。 

执行TOUSER命令的数据库用户需拥有DBA权限。 

TOUSER命令需与FROMUSER命令配套使用，且Value的个数需保持一致。 

在执行本命令导入前，要求目标端YashanDB上已创建同名表空间及指定的TOUSER用户；若导入对象已经存在，则导入失败。 

示例 

```sql
-- 创建普通用户sales1
CREATE USER sales1 IDENTIFIED BY sales1;
GRANT CREATE SESSION TO sales1;

-- 向sales1用户导入sales用户数据
$ imp sales0/sales0@127.0.0.1:1688 file=export.owner.export fromuser=sales touser=sales1

-- FROMUSER的Value值会去重，TOUSER的Value值不去重，需保证去重后的FROMUSER的Value个数与TOUSER的Value个数相同
$ imp sales0/sales0 file=export.owner.export fromuser=sales,sales touser=sales1,sales1
YAS-08025, invalid value of parameter FROMUSER/TOUSER.
import terminated unsuccessfully
```

### TABLES模式

TABLES模式用于导入指定的表数据，包括：

- 表的元数据
- 基于表的索引，注释，约束，列属性等信息
- 表数据
- 表的对象权限

本模式命令的Value值为表名称，可以为多个，输入重复表名时将只导入一次。若指定的表不在数据文件中，则提示Warning。

对于执行本模式导入的数据库用户，如其为导入表的OWNER，则对其无权限要求；否则，要求其拥有DBA权限，且必须通过FROMUSER指定导入表的OWNER。

在执行本模式导入之前，要求同名的数据库、表空间及用户已存在；若导入对象已经存在，则导入失败。

如果导入表存在依赖关系，且依赖对象在数据库里不存在，将无法导入成功。

示例

```sql
-- 删除现有的sales_info表
DROP TABLE IF EXISTS sales_info;

-- 执行表导入
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.table.export TABLES=sales_info FROMUSER=sales

-- 以SALES用户登录，统计sales_info表相关数据
SELECT COUNT(1) constriant_count 
FROM user_constraints 
WHERE table_name='SALES_INFO';
     CONSTRIANT_COUNT 
--------------------- 
                    4
SELECT COUNT(1) index_count
FROM user_indexes 
WHERE table_name='SALES_INFO';
          INDEX_COUNT 
--------------------- 
                    1
SELECT COUNT(1) part_count 
FROM user_tab_partitions 
WHERE table_name='SALES_INFO';
           PART_COUNT 
--------------------- 
                    3
SELECT COUNT(1) data_count FROM sales_info;
           DATA_COUNT 
--------------------- 
                    6
```

### IGNORE

imp导入过程中会执行创建对象的操作，可能会出现要创建的对象已存在的错误，IGNORE用于标识系统对该类错误的处理方式：

- 若IGNORE为Y：表示可以忽略这个错误，继续导入该对象关联的数据内容。
- 若IGNORE为N：表示不忽略这个错误，输出对象已存在的告警并跳过该对象相关的数据内容，进入下个对象的处理。默认值为N。

示例

```sql
-- 执行表导入
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.table.export TABLES=sales_info FROMUSER=sales IGNORE=y
```

### ROWS

本选项用于指定是否导入表数据，为Y时表示同时导入表数据和元数据，为N时表示仅导入元数据，可省略，省略则默认为Y。

### DATA_ONLY

本选项用于指定是否仅导入表数据，为Y时表示仅导入表数据，不会导入元数据（索引、约束等），为N时无实际作用，可省略，省略则默认为N。

请确保ROWS=Y时才能指定DATA_ONLY=Y，否则返回错误。

示例

```shell
# 仅导入表数据
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=Y DATA_ONLY=Y

# 同时导入元数据及表数据
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=Y DATA_ONLY=N

# 仅导入元数据
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=N DATA_ONLY=N

# 返回错误
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export FROMUSER=sales ROWS=N DATA_ONLY=Y
```

### TRUNCATE

用于指定导入文件中待导入的表已在目标数据库中存在时是否直接覆盖（覆盖表示先自动删除原有数据，再导入文件中的相应数据），可省略。仅导入元数据时，本选项不生效。

- TRUNCATE=N，表示不覆盖，默认为N。
- TRUNCATE=Y，表示直接覆盖。如果待导入的表存在外键约束，可能会truncate失败，此种情况下会跳过失败的表继续后续的导入操作。
  - 配置TRUNCATE=Y时，会默认设置IGNORE=Y，表示如果元数据导入冲突则跳过继续执行导入表数据以及索引约束等。若配置TRUNCATE=Y时手动修改IGNORE=N，则返回错误。
  - 同时配置TRUNCATE=Y，DATA_ONLY=Y，表示只对表数据truncate后进行导入，跳过索引约束等。

示例

```shell
# 导入元数据及表数据，自动忽略元数据冲突
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export TRUNCATE=Y

# 导入表数据
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export ROWS=Y DATA_ONLY=Y TRUNCATE=Y

# 返回错误
$ imp sales0/sales0@127.0.0.1:1688 FILE=export.owner.export IGNORE=N TRUNCATE=Y
```
