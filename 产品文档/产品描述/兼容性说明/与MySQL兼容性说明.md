YashanDB提供了MySQL数据库的兼容性功能，但由于不同数据库的底层架构、产品形态等存在差异，导致适配MySQL数据库的某些特性与适配Oracle数据库的某些特性不能互相兼容。YashanDB通过控制开关的方式，很好地解决了这个问题，从而可以让用户从不同数据库向YashanDB迁移时，无需进行大量地SQL校对和改写工作。

YashanDB通过语法模式选择决定兼容MySQL适配度，用户在安装数据库时可按需选择：

- [mysql模式](#COMPAT_VECTOR)：仅适用于单机部署，在该模式下用户输入的SQL语句将优先按照MySQL的语法体系进行解析，适用于需要密切适配MySQL数据库的使用场景。

- yashan模式：在该模式下使用YashanDB的语法体系。若安装时指定为yashan模式（省略不指定时，默认为yashan模式），安装后无法切换为mysql模式，此时只能使用扩展支持的部分MySQL特有语法，适用于只需偶尔适配MySQL数据库的使用场景，详细介绍请查阅[yashan模式兼容部分MySQL语法](#SQL_PLUGIN)。

<span id="COMPAT_VECTOR" name="COMPAT_VECTOR" class="yaslink"></span>

## mysql模式

在mysql模式下，用户输入的SQL语句将优先按照MySQL的语法体系进行解析，还支持MySQL专有语句，例如show语句、use语句等。

### 控制开关

在YashanDB安装过程中，通过yasboot package se gen命令的mode参数可以指定语法模式为mysql模式。

以mysql模式安装后，在数据库OPEN阶段新建的所有会话默认为mysql模式，在NOMOUNT或MOUNT阶段创建的会话则仍为yashan模式，可通过以下方式查看当前会话的语法模式。

```sql
-- 查询COMPAT_VECTOR参数值
SHOW PARAMETER COMPAT_VECTOR

name                                                             value                                                  
---------------------------------------------------------------- ----------------------------------------------------------------
COMPAT_VECTOR                                                    mysql            
```

在mysql模式下，部分数据库管理操作（例如主备切换）无法正常执行，需要先将当前会话切换至yashan模式（不影响其他会话）再执行语句/命令，且换方式如下：

```sql
-- 切换到yashan模式，再执行相关运维操作
ALTER SESSION SET COMPAT_VECTOR = yashan;

-- 运维操作完成后，再切换回mysql模式继续进行业务相关操作
ALTER SESSION SET COMPAT_VECTOR = mysql;
```

### 概念介绍

| 名词               | yashan模式（同Oracle）             | mysql模式                    |
|--------------------|-----------------------|---------------|
| 数据库（Database） | 由表空间、数据文件等组成的完整实例<br/>一套YashanDB环境就是1个Database | 数据库对象（表、视图等）的逻辑容器 |
| 模式（Schema） | 与用户一一对应，是用户的数据库对象（表、视图等）的逻辑容器<br/>创建用户时，自动创建1个同名schema | 数据库的别名  |
| 用户（User） | 登录账号 + 同名模式拥有者（天然具备该模式下对象的全部权限）<br/>可被授予其他模式对象的权限 | 仅为登录账号<br/>可被授予任意数据库的权限 |
| 角色（Role） | 权限的集合 | 无<br/>可以使用YashanDB内置的角色（使用时基于yashan语法解析相关SQL语句） |

### 具体兼容项

#### 通信协议兼容

以mysql模式安装YashanDB数据库管理系统，会默认开启单独的监听端口，处理通过MySQL协议向YashanDB服务端发起的连接、执行等协议命令。

#### 语法兼容

在YashanDB的语法体系中，SELECT语句必须包含FROM子句，而MySQL的语法允许SELECT语句不带FROM子句。切换成mysql模式后，执行以下语句，将返回当前会话的登录用户名，而不是报错：

```SQL
SELECT USER();
```

当会话的语法模式切换为MySQL后，语法解析、语句执行将按照MySQL 5.7的风格。与YashanDB可能存在以下差异：

- **词法**

    例如mysql模式下，反引号（`）表示对象名，单引号（'）表示字符串，双引号（"）默认表示字符串，但在sql_mode中包含ANSI_QUOTES时，表示对象名。而yashan模式下，双引号表示对象，单引号表示字符串。

- **对象的概念**

    例CREATE DATABASE语句，在mysql模式下表示创建一个Schema。

- **数据类型**

    例如Bool类型，在mysql模式下表示Tinyint(1)。

- **字面量的数据类型**

    字符类型的字面量，在mysql模式下，其数据类型为VARCHAR（与MySQL兼容），在Yashan模式下，类型为CHAR（与Oracle兼容）。

- **数据定义语言**
    
    例如CREATE TABLE语句，在mysql模式下会兼容Engine、Character set等选项，不再支持pctfree等选项。

- **数据操作语言**
    
    例如mysql模式下支持SELECT语句不带FROM子句。

- **权限**

    例如执行SELECT FOR UPDATE语句，在mysql模式下需要用户具有表的读权限，以及插入、删除、更新三者之中任意一种权限。而在yashan模式下，SELECT语句需要读（READ）权限，SELECT FOR UPDATE语句需要单独的SELECT权限。

- **字符序**

    mysql模式下，字符类型的比较、排序规则受字符序影响（例如，数据库字符集选择为UTF8MB4时，默认的字符序为UTF8MB$_GENERAL_CI，字符类型的排序和比较不区分大小写、忽略末尾空格），而在Yashan模式下，则按照二进制的方式比较和排序（区分大小写、末尾空格参与比较）。

> **Caution**:
>
> - YashanDB 23.4暂未覆盖MySQL的所有语法及行为，对于暂未覆盖的那部分语句，在mysql模式下执行时仍会按照YashanDB语法进行解析和执行。
>
>- 对于MySQL本身不支持的YashanDB功能，这类语句在mysql模式下并不会产生歧义，因此在mysql模式下仍可以按照YashanDB语法进行解析和执行，例如查询语句的集合操作（MINUS、INTERSECTS）、层次查询（CONNECT BY）。

#### 保留字兼容

YashanDB兼容了MySQL数据库大部分保留字，但目前个别保留字仍存在一定区别，包括：

- CONNECT
- EXCEPT
- IF
- INTERSECT
- MINUS
- OF
- PUBLIC
- ROW
- ROWS
- START
- SYSDATE

### 规格差异

YashanDB数据库在mysql模式下，绝大多数规格与yashan模式保持一致，以下仅列出与yashan模式下存在差异的规格。

#### 对象规格

| 规格名称            | 规格类型 | 最大值                                     |
| ------------------- | -------- | ------------------------------------------ |
| user数量            | 最大值   | 10240 - 内置user数量 - database/schema数量 |
| database\schema数量 | 最大值   | 10240 - 内置user数量 - 普通user数量        |
| user名称长度        | 最大值   | 60Bytes                                    |

#### 数据类型规格

详情请查阅[数据类型（mysql模式）](../../开发手册/SQL参考手册/数据类型（mysql模式）/00数据类型（mysql模式）)。

### 功能约束

使用mysql模式时，存在如下约束：

| 约束项                  | 约束行为                                                                                                                                          |
| ----------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------|
| 部署形态       |仅单机部署可选部署为mysql模式。                                                                                                                                     |
| 表类型                  | mysql模式下仅支持创建行存表。                                                                                                                           |
| 用户登录                | * 如需使用yashan模式下创建的用户登录YashanDB（mysql模式），则要求客户端支持SHA256插件。<br/>* 如需使用yasql以mysql模式下创建的用户登录YashanDB（mysql模式）需要使用双引号将用户名包围。                                                                           |
| 用户删除和修改          | 不允许在mysql模式下删除和修改yashan模式下创建的用户。                                                                                                          |
| 对象名称                | lower_case_table_names参数仅支持设置为1。<br/>schema、表、视图、表别名的名称按照小写展示，列名按照大写展示。<br/>对象名称匹配按照大小写不敏感的方式匹配。                                                                      |
| Binary/Blob类型文本协议 | 当客户端与服务端字符集不一致时，会按服务端字符集转码，可能与客户端预期不一致。                                                                                                       |
| 字符集                  | 仅支持实例级字符集设置，schema/table/column级的字符集均只为语法兼容。                                                                                                  |
| 排序集                  | 仅支持实例级排序集设置，schema/table/column级的排序集均只为语法兼容。                                                                                                  |
| 全局变量                | 全局变量查询和设置仅为语法兼容，除字符集、自动提交外，大部分实际不会生效。                                                                                                         |
| SQL_MODE                | 除ANSI_QUOTES、NO_BACKSLASH_ESCAPES、PIPES_AS_CONCAT、REAL_AS_FLOAT、PAD_CHAR_TO_FULL_LENGTH外，其它SQL_MODE无论是否设置，均不会对数据库的行为产生影响，实际执行效果与yashan模式一致。 |
| 可执行注释              | 可执行注释在mysql模式下会被视为注释，对语句执行不产生影响。                                                                                                            |

<span id="SQL_PLUGIN" name="SQL_PLUGIN" class="yaslink"></span>

## yashan模式兼容MySQL语法

yashan模式是YashanDB的默认语法模式，若安装时指定为yashan模式（省略不指定时，默认为yashan模式），安装后无法切换为mysql模式，此时只能使用扩展支持的部分MySQL特有语法。

### 控制开关

在yashan模式下开启兼容MySQL语法后，可以便捷地使用在Oracle中不可用但在MySQL中可用的功能，不开启时仅此类相冲突的功能不可用，其他正常功能不受影响。

YashanDB使用配置参数SQL_PLUGIN控制是否开启MySQL兼容的开关，默认值为NONE，即默认不开启MySQL兼容，将参数值设为MySQL表示开启MySQL语法兼容。

```sql
-- 开启MySQL语法兼容
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL';
或
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = MEMORY;
或
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = SPFILE;

-- 关闭MySQL语法兼容
ALTER SYSTEM SET SQL_PLUGIN = 'NONE';
或
ALTER SYSTEM SET SQL_PLUGIN = 'NONE' SCOPE = MEMORY;
或
ALTER SYSTEM SET SQL_PLUGIN = 'NONE' SCOPE = SPFILE;
```

其中，SCOPE=MEMORY表示仅在当前实例运行期间配置生效；SCOPE=SPFILE表示当前实例运行期间不生效，仅在重启后配置生效；不指定SCOPE表示配置立即生效，且实例重启后仍生效。

### 影响范围

在yashan模式下开启兼容MySQL语法开关后，系统将发生如下变化：

| 特性                        | 开启前 | 开启后 |
| --------------------------- | ------ | ------ |
| 多表DELETE                  | 不支持 | 支持   |
| 多表UPDATE                  | 不支持 | 支持   |
| DELETE table语法            | 支持   | 不支持 |
| DELETE table FROM table语法 | 不支持 | 支持   |

示例（单机HEAP表）

```sql
-- employees为一张员工信息表，包含如下五条数据
SELECT BRANCH,DEPARTMENT,EMPLOYEE_NO,EMPLOYEE_NAME,SEX,ENTRY_DATE FROM employees;
BRANCH DEPARTMENT EMPLOYEE_NO   EMPLOYEE_NAME SEX   ENTRY_DATE                       
------ ---------- ------------- ------------- ----- -------------------------------- 
0101   000        0101000001    Mask          1     2020-09-09 22:55:32                   
0101   000        0101000002    John          1     2017-12-14 22:55:32                   
0201   010        0201010011    Anna          0     2022-08-10 22:55:32                   
0201   008        0201008003    Jack          1     2021-07-06 22:55:32                   
0101   008        0201008004    Jim           1     2022-11-18 22:55:32                  
 
-- 创建与employees同构的employees2表
DROP TABLE IF EXISTS employees2;
CREATE TABLE employees2 AS SELECT * FROM employees WHERE 1=2;
INSERT INTO employees2 VALUES ('0101','008','0201008003','Jim','0',DATE '2021-11-17');
INSERT INTO employees2 VALUES ('0101','000','0101000002','John','1',DATE '2021-11-17');
COMMIT;
 
-- 开启前
UPDATE employees,employees2 SET EMPLOYEES.EMPLOYEE_NAME = 'TOM',EMPLOYEES2.EMPLOYEE_NAME = 'TOM' WHERE EMPLOYEES.SEX = EMPLOYEES2.SEX;
YAS-04344 multi-table update is not supported
 
DELETE FROM employees,employees2;
YAS-04345 multi-table delete is not supported
 
DELETE employees;
 
DELETE employees FROM employees;
[1:18]YAS-04209 unexpected word FROM
 
-- 开启后
ROLLBACK;
ALTER SYSTEM SET SQL_PLUGIN = 'MYSQL' SCOPE = MEMORY;
 
UPDATE employees,employees2 SET EMPLOYEES.EMPLOYEE_NAME = 'TOM',EMPLOYEES2.EMPLOYEE_NAME = 'TOM' WHERE EMPLOYEES.SEX = EMPLOYEES2.SEX;
 
DELETE FROM employees,employees2;
 
DELETE employees;
[1:17]YAS-04209 unexpected word employees
 
DELETE employees FROM employees;
```

### 具体兼容项

本文将从如下方面具体说明，在单机部署和行式存储中，YashanDB的yashan模式对MySQL数据库的兼容情况：

- SQL语法
- 表达式运算
- FILTER CONDITION
- 数据类型
- 内置函数
- PL
- 系统视图
- 字符集
- SQL引擎
- 数据库安全
- 保留字

#### SQL语法

YashanDB的yashan模式支持MySQL数据库中主流的SQL语法，其他少数因功能性缺失导致的不兼容将报语法不支持错误，此时应联系我们的技术支持提供变通方案。

**（1）DML类**

**SELECT**

- 支持大部分查询功能，包括单表查询，多表查询，子查询，内连接，半连接，外连接，分组及聚合，层次查询等
- 支持UNION、UNION ALL等集合操作
- 支持EXPLAIN方式查看执行计划
- 支持随机抽样查询能力

**INSERT**

- 支持单行，多行插入，同时支持指定分区插入
- 支持INSERT INTO SELECT语句
- 支持单表插入
- 支持INSERT ON DUPLICATE KEY UPDATE语法

**UPDATE**

- 支持单列和多列更新
- 支持使用子查询

**DELETE**

- 支持单表和多表删除
- 支持使用子查询

**（2）DDL类**

**HEAP表**

- CREATE TABLE/CREATE TABLE AS
- ALTER TABLE
- DROP TABLE
- TRUNCATE TABLE

**临时表**

- 支持临时表的创建与删除

**分区表**

- 支持RANGE、LIST、HASH分区
- 支持ADD|DROP|TRUNCATE PARTITION

**约束**

- 包括in_line约束、out_of_line约束
- UNIQUE、PRIMARY KEY、FOREIGN KEY、CHECK、(NOT)NULL类型约束

**视图**

- CREATE VIEW
- DROP VIEW
- 视图支持SELECT/INSERT/UPDATE/DELETE

**BTree索引**

- 包括唯一索引和非唯一索引

#### 表达式运算

YashanDB的yashan模式包含了主流的计算框架实现对表达式的运算，详情如下表所示：

| 表达式运算类型 | YashanDB | MySQL |
| :------------- | :------- | :---- |
| 二元运算加法   | 支持     | 支持  |
| 二元运算减法   | 支持     | 支持  |
| 二元运算乘法   | 支持     | 支持  |
| 二元运算除法   | 支持     | 支持  |
| 二元运算取余   | 支持     | 支持  |
| 一元运算取反   | 支持     | 支持  |
| 位运算与       | 支持     | 支持  |
| 位运算或       | 支持     | 支持  |
| 位运算异或     | 支持     | 支持  |

#### FILTER CONDITION

YashanDB的yashan模式中，FILTER CONDITON类型除了ROWNUM外，完全兼容MySQL数据库的FILTER CONDITON类型，详情如下表所示：

| filter               | YashanDB | MySQL  |
| :------------------- | :------- | :----- |
| ALL                  | 支持     | 支持   |
| AND                  | 支持     | 支持   |
| ANY                  | 支持     | 支持   |
| BEWTEEN...AND...     | 支持     | 支持   |
| EXISTS               | 支持     | 支持   |
| GREAT EQUAL          | 支持     | 支持   |
| GREATE               | 支持     | 支持   |
| IN                   | 支持     | 支持   |
| IS NOT NULL          | 支持     | 支持   |
| IS NULL              | 支持     | 支持   |
| LESS                 | 支持     | 支持   |
| LESS EQUAL           | 支持     | 支持   |
| LIKE                 | 支持     | 支持   |
| NOT                  | 支持     | 支持   |
| NOT BEWTEEN...AND... | 支持     | 支持   |
| NOT EQUAL            | 支持     | 支持   |
| NOT EXISTS           | 支持     | 支持   |
| NOT IN               | 支持     | 支持   |
| NOT LIKE             | 支持     | 支持   |
| NOT RLIKE            | 支持     | 支持   |
| OR                   | 支持     | 支持   |
| EQUAL                | 支持     | 支持   |
| RLIKE                | 支持     | 支持   |
| ROWNUM               | 支持     | 不支持 |
| SOME                 | 支持     | 支持   |

#### 数据类型

YashanDB的yashan模式实现的数据类型与MySQL数据库对比情况如下表所示：

| 数据类型               | YashanDB | MySQL  |
| :--------------------- | :------- | :----- |
| BOOLEAN                | 支持     | 支持   |
| TINYINT                | 支持     | 支持   |
| SMALLINT               | 支持     | 支持   |
| MEDIUMINT              | 不支持   | 支持   |
| INTEGER                | 支持     | 支持   |
| BIGINT                 | 支持     | 支持   |
| FLOAT                  | 支持     | 支持   |
| DOUBLE                 | 支持     | 支持   |
| NUMBER                 | 支持     | 不支持 |
| DECIMAL / NUMERIC      | 不支持   | 支持   |
| DATE                   | 支持     | 支持   |
| DATETIME               | 不支持   | 支持   |
| TIMESTAMP              | 支持     | 支持   |
| TIME                   | 支持     | 支持   |
| YEAR                   | 不支持   | 支持   |
| INTERVAL YEAR TO MONTH | 支持     | 不支持 |
| INTERVAL DAY TO SECOND | 支持     | 不支持 |
| CHAR                   | 支持     | 支持   |
| VARCHAR                | 支持     | 支持   |
| RAW                    | 支持     | 不支持 |
| CLOB                   | 支持     | 不支持 |
| TINYBLOB               | 不支持   | 支持   |
| BLOB                   | 支持     | 支持   |
| MEDIUMBLOB             | 不支持   | 支持   |
| TINYTEXT               | 不支持   | 支持   |
| TEXT                   | 不支持   | 支持   |
| LONGTEXT               | 不支持   | 支持   |
| BIT                    | 不支持     | 支持   |
| ROWID                  | 支持     | 不支持 |
| JSON                   | 支持     | 支持   |

#### 内置函数

YashanDB的yashan模式实现的内置函数与MySQL数据库的内置函数对比情况如下表所示：

| 函数类型              | 支持函数列表                                                 | YashanDB | MySQL  |
| :-------------------- | :----------------------------------------------------------- | :------- | :----- |
| 数学运算函数          | ABS、ACOS、ASIN、ATAN、ATAN2、AVG、CEIL/CEILING、COS、COT、DIV、FLOOR、MOD、PI、POW/POWER、RANDOM/RAND、SIGN、SIN、SQRT、TAN、TRUNCATE/TRUNC | 支持     | 支持   |
| 字符处理函数          | ASCII、BIT_LENGTH、CHR/CHALEASTR 、CHAR_LENGTH/CHARACTER_LENGTH、CONCAT、CONCAT_WS、FIND_IN_SET、GROUP_CONCAT、INSTR、 LCASE/LOWER、 LEFT、LENGTH、LPAD、LTRIM、POSITION、OCTET_LENGTH、RIGHT、RPAD、RTRIM、REPLACE、SUBSTR、SUBSTRING、SUBSTRING_INDEX、TRIM、 UCASE/UPPER | 支持     | 支持   |
| 正则匹配函数          | REGEXP_LIKE、REGEXP_REPLACE、REGEXP_INSTR、REGEXP_SUBSTR     | 支持     | 支持   |
| 转换函数              | BIN、CAST                                                    | 支持     | 支持   |
| 聚集函数              | AVG、COUNT、GROUP_CONCAT、MAX、MIN、STDDEV、STDDEV_POP、STDDEV_SAMP、SUM、VAR_POP、VAR_SAMP、VAR_SAMP、VARIANCE | 支持     | 支持   |
| 窗口函数              | FIRST_VALUE、LAG、LAST_VALUE、LEAD、RANK、ROW_NUMBER         | 支持     | 支持   |
| 时间处理函数          | CURRENT_TIMESTAMP、DATE、DAYOFWEEK、DATE_FORMAT、DATE_ADD、DATE_SUB、EXTRACT、LAST_DAY、LOCALTIMESTAMP、NOW、SYSDATE、TIME、TIMESTAMP、TIMEDIFF、TIMESTAMPDIFF、UTC_TIMESTAMP | 支持     | 支持   |
| 条件处理函数          | CASE、IF、IFNULL                                             | 支持     | 支持   |
| JSON处理函数          | JSON、JSON_ARRAY_GET、JSON_ARRAY_LENGTH、JSON_EXISTS、JSON_FORMAT、JSON_PARSE、JSON_QUERY、JSON_SERIALIZE | 支持     | 不支持 |
| MySQL information函数 | BENCHMARK、CHARSET、COERCIBILITY、COLLATION、CONNECTION_ID、 CURRENT_ROLE、CURRENT_USER、DATABASE、ICU_VERSION、ROLES_GRAPHML、ROW_COUNT、SCHEMA、SESSION_USER、USER、VERSION | 不支持   | 支持   |
| MySQL加密和压缩函数  | AES_DECRYPT、AES_ENCRYPT、COMPRESS、RANDOM_BYTES、SHA、SHA1、SHA2、STATEMENT_DIGEST、 STATEMENT_DIGEST_TEXT、UNCOMPRESS、UNCOMPRESSED_LENGTH | 不支持   | 支持   |
| 其他函数              | BITAND/BIT_AND、 BITOR/BIT_OR、BITXOR/BIT_XOR、COALESCE、ISNULL、GREATEST、LAST_INSERT_ID、LEAST、SOUNDEX、MD5 | 支持     | 支持   |

#### PL

YashanDB的yashan模式兼容了MySQL数据库大部分的PL功能，但语法格式上有一定的区别，包括：

- 数据类型
- 流程控制
- 静态SQL
- 动态SQL
- 异常处理
  - 系统定义异常
  - 用户自定义异常
- 游标
- 存储过程
- 触发器
  - 支持行级触发器，但不支持语句级触发器
  - 目前仅支持在表上创建触发器，不支持在视图上创建触发器
- 用户自定义函数（SQL语言的UDF）
- JOB

#### 字符集

YashanDB的yashan模式目前支持以下字符集：

| 字符集      | YashanDB | MySQL  |
|:---------| :------- | :----- |
| ASCII    | 支持     | 支持   |
| GBK      | 支持     | 支持   |
| UTF-8    | 支持     | 支持   |
| ISO88591 | 支持     | 不支持 |
| GB18030  | 支持     | 支持 |

同时支持如下字符集排序方式：

- ASCII_GENERAL_CS
- ASCII_GENERAL_CI
- GBK_GENERAL_CS
- GBK_GENERAL_CI
- UTF8_GENERAL_CS
- UTF8_GENERAL_CI
- UTF8_PINYIN_CS
- UTF8_PINYIN_CI
- ISO88591_GENERAL_CS
- ISO88591_GENERAL_CI
- GB18030_GENERAL_CS
- GB18030_GENERAL_CI
- GB18030_PINYIN_CS
- GB18030_PINYIN_CI

#### SQL引擎

YashanDB的yashan模式下，SQL引擎兼容了MySQL数据库大部分的特性，包括：

- 支持查询改写
- 支持预编译语句
- 支持基于成本的优化器
- 支持计划生成与展示（EXPLAIN）
- 支持执行计划缓存
- 支持执行计划快速参数化
- 支持Optimizer Hint
- 支持like和reglike的模糊匹配能力

#### 保留字兼容

YashanDB的yashan模式兼容了MySQL数据库大部分保留字，但目前个别保留字仍存在一定区别，包括：

- CONNECT
- EXCEPT
- IF
- INTERSECT
- MINUS
- OF
- PUBLIC
- ROW
- ROWS
- START
- SYSDATE
