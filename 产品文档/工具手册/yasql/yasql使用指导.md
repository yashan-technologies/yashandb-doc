## 查看帮助和版本信息


使用yasql -H命令可查看帮助和版本信息。

```shell
$ yasql -H
```

使用yasql -V命令可查看版本信息。

```shell
$ yasql -V
```

## 命令格式

```shell
$ yasql [-S] username/password@host:port [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]] 
$ yasql [-S] / as sysdba [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]]
$ yasql <command>
```

**-S**

用于指定[静默登录](#login)，需确保-S为首个参数，该参数不区分大小写。

**username/password**、**/**

指定[登录或连接](#login)数据库的数据库用户名和密码。

若用户名或密码中存在特殊符号（下划线除外），例如`@`、`/`、`.`、`!`、`$`、`'`等，[创建](../../开发手册/SQL参考手册/SQL语句（yashan模式）/CREATE USER)/[管理](../../开发手册/SQL参考手册/SQL语句（yashan模式）/ALTER USER)用户等操作中指定相应值时需使用双引号包围。登录数据库时，为确保yasql能正确向数据库服务端转递用户名和密码的值，建议[分步登录](#nolog)，此类用户名或密码只需使用双引号包围即可，若直接登录则需对其进行相应改写：

- 如果用户名或密码中本身不存在单引号，直接使用单引号包围即可进行传递，例如`user@name`、`password$`需转义改写为`'"user@name"'/'"password$"'`。

- 如果用户名或密码中本身已存在单引号，则无法再使用单引号进行传递，需酌情使用转义符和双引号：

  - 如果同时存在单引号和Linux系统等环境下的功能符号，例如变量替换符号、变量替换符、转义符等，即`\`、`$`、`"`、`'`，需使用多重转义，例如`user'1$name`、`pass'1\word`需转义改写为`"\"user'1\$name\""/"\"pass'1\\word\""`。

  - 如果存在单引号且不存在上述功能符号，则需要叠加使用双引号，例如`user'name`、`password`需转义改写为`"\"user'name\""/password`。

> **Note**：
>
> 为正确将用户名传递至数据库服务端，使用yasql以mysql模式下创建的用户连接YashanDB服务端（mysql模式）时需额外使用双引号将用户名包围。

通过操作系统认证方式登录时，可省略用户名和密码，但不能省略/。操作系统认证方式登录需配置后才可使用，配置操作请查阅数据库管理手册中的[操作系统身份认证配置](../../数据库管理/基本数据库管理/操作系统身份认证配置)。

**@host:port**

指定需登录或连接的目标数据库的URL，详情请查阅[登录与连接](#login)。

**as sysdba**

通过操作系统认证方式登录时的标识。

**-c "SQL"**

用于登录并执行单条SQL语句（执行完后立即退出），详情请查阅[执行SQL语句](#singlesql)。

**-f [-e] filename[.ext]**

用于执行SQL文件，详情请查阅[执行SQL文件](#fsql)。

**@filename[.ext]**

用于执行SQL文件，详情请查阅[执行SQL语句](#sqlfile)。

**command**

具体的命令语句，包括[/nolog](#nolog)、set命令等。

## 功能介绍

<span id="login" name="login" class="yaslink"></span>
### 登录与连接

登录方式可分为以下几种：

- 方式一：直接登录，命令格式为`yasql username/password@host:port`。

- 方式二：静默登录，命令格式为`yasql -S username/password@host:port`，使用该方式登录并连接数据库不返回命令行提示、命令回显信息、登录提示信息和版本信息。

- 方式三：操作系统认证登录，命令格式为`yasql / as sysdba`或`yasql username/password as sysdba`，使用该方式登录无需配置连接方式，只能登录到本地YASDB_DATA环境变量对应的数据库实例。
<span id="nolog" name="nolog" class="yaslink"></span>
* 方式四：分步登录：
    1. 使用/nolog命令启动yasql，命令格式为`yasql /nolog`，此时仅打开登录窗口但不连接数据库。

    2. 使用connect或conn命令连接并登录数据库，命令格式为`connect username/password@host:port`或`conn username/password@host:port`。

- 方式五：切换登录，即登录后使用connect或conn命令切换至其它用户登录。

连接方式可分为以下几种：

- 方式一：有URL的IPv4连接，例如`@192.168.1.2:1688`。

- 方式二：有URL的IPv6连接，IP必须使用中括号包围，链路本地IPv6地址还需使用百分号标识网络接口名。系统网卡需要支持IPv6的连接，目前支持：

  - IPv6环回地址，例如`@[::1]:1688`

  - 链路本地地址，例如`@[fe80::a89b:8fdb:2c28:35ce%ens192]:1688`

  - 唯一本地地址，例如`@[fc00:7::126]:1688`

  - 未指定地址，例如`@[::]:1688`

  - 全球单播地址，例如`@[2001:250:4000:4000:f80f:6d4c:4310:35ce]:1688`

  - IPv4映射的IPv6地址，例如`@[::ffff:192.168.7.126]:1688`

- 方式三：有URL的域名连接，例如`@yashandb123.vip:1688`。域名需支持解析到存在数据库服务器的IP地址，若当前域名解析到多个IP地址，则将按照解析顺序依次尝试连接这些IP地址直至成功。

- 方式四：无URL的连接，即采用[UDS本地连接](../../产品安全/连接管理/00连接管理)，表示连接到本地$YASDB_DATA环境变量对应的数据库实例。使用该方式连接数据库属于高权限操作，请合理使用。

- 方式五：别名连接，例如`@alias`，需先在${YASDB_HOME}/client/yasc_service.ini中定义别名。

示例

```shell
# 直接登录+有URL的IPv4连接方式
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {版本号} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
SQL>

# 直接登录+有URL的IPv6环回地址连接方式
$ yasql sales/********@[::1]:1688
YashanDB SQL Enterprise Edition Release {版本号} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
SQL>

# 静默登录+有URL的域名连接方式
$ yasql -S sales/sales@123.com:1688

# 操作系统认证登录
$ yasql / as sysdba
YashanDB SQL Enterprise Edition Release {版本号} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
SQL>

# 直接登录+别名连接
$ cat ${YASDB_HOME}/client/yasc_service.ini
local = 127.0.0.1:1688
$ yasql sales/********@local
Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
SQL>

# 分步登录
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {版本号} x86_64
SQL> conn sales/sales@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {版本号} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
-- 登录成功

-- 切换登录：通过用户密码+无URL的连接方式切换至另一个用户
SQL> conn sales1/sales1

-- 切换登录：通过用户密码+有URL的IPv4连接方式切换至另一个用户
SQL> conn sales1/sales1@192.168.1.3:1688
```

### 执行SQL语句

**方式一**：登录后执行SQL语句

登录数据库后，在yasql命令行界面可直接执行SQL语句。

示例

```shell
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {版本号} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
SQL> CREATE TABLE customer(cno INT, cname VARCHAR(50));
 
 
SQL> INSERT INTO customer VALUES(1, 'customer1');
 
 
SQL> SELECT cno,cname FROM customer;
 
         CNO CNAME                                                
------------ -----------------------------------------------------
           1 customer1                                           
 
```

<span id="singlesql" name="singlesql" class="yaslink"></span>
**方式二**：执行单条SQL语句并退出

通过参数-c "SQL"可以直接在操作系统层面连接登录到数据库执行单条SQL语句，执行完后不保留登录状态（直接退出）。

示例

```sql
$ yasql sales/******** -c "select 1 from dual";
           1
------------
           1
```

### 执行SQL文件
<span id="fsql" name="fsql" class="yaslink"></span>
**方式一**：通过-f\[-e\]参数

通过参数-f \[-e\] filename[.ext]可以直接在操作系统层面连接登录到数据库执行一个SQL文件，后缀名可省略。

- -f用于指定需执行的SQL文件。

- -e用于显示文件中被执行的具体语句。

当SQL文件位于yasql工具同目录时无需指定文件路径，否则需指定文件所在路径（绝对路径或相对路径均可）。

示例

```sql
-- s.sql在yasql同目录下
$ yasql sales/******** -f s.sql
SQL> SELECT 1 FROM dual;
 
           1
------------
           1
 
 
SELECT 2 FROM dual;
 
           2
------------
           2
 
```

<span id="sqlfile" name="sqlfile" class="yaslink"></span>
**方式二**：通过@或@@方式

通过@filename[.ext]或@@filename[.ext]命令可以执行本地SQL文件，后缀名可省略。具体使用方式如下：

- 登录数据库后，在yasql命令行界面直接执行`@filename[.ext]`或`@@filename[.ext]`。

- 直接在操作系统层面执行`yasql username/password@host:port @filename[.ext]`或`yasql username/password@host:port @@filename[.ext]`连接登录到数据库执行本地SQL文件，执行完后会保留登录状态和yasql命令行界面。

在以交互方式输入`@file_name.ext`或`@@file_name.ext`时，@或@@会在当前工作目录下查找所需要被调用的文件。当SQL文件位于yasql工具当前工作目录时无需指定文件路径，否则需指定文件所在路径（绝对路径或相对路径均可）。

在不存在脚本嵌套调用时，@与@@命令等价。

若脚本s_1.sql中含有@@ s_2.sql的命令，使用@或@@方式调用s_1.sql脚本嵌套调用s_2.sql时，@@会在与s_1.sql相同的路径（而非当前工作目录）中查找嵌套脚本s_2.sql。

示例

```sql
-- s.sql在yasql同目录下
@s.sql
 
           1
------------
           1
 
 
 
           2
------------
           2
 
 
 
-- 绝对路径
@/home/yasdb/s.sql
 
           1
------------
           1
 
 
 
           2
------------
           2
 
 
-- 相对路径
@../s.sql
 
           1
------------
           1
 
 
 
           2
------------
           2
 
```

### 输出保存为文件

SPOOL命令用于将yasql会话中的输出内容保存到文件中，命令格式：

```sql
SPO[OL] [file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]] | OFF]
```

**file_name[.ext]**

指定用于保存输出结果的文件名，名称规则如下：

- 若file_name包含空格，则必须使用引号包围file_name。
- 文件后缀名可省略，省略后默认拓展名为`.LST`（Windows系统中为全大写）或`.lst`（Linux系统中为全小写）。

文件后缀名不会附加到/dev/null和/dev/stderr等系统文件。

**CRE[ATE]**

创建指定名称的新文件，此时file_name[.ext]不能与已有文件重名。

**REP[LACE]**

使用输出结果的文件替换已有文件，此时file_name[.ext]需指定为已有文件的名称。

当未指定[CRE[ATE] | REP[LACE] | APP[END]]选项时，默认为REP[LACE]，即`SPO[OL] [file_name[.ext]`等同于`SPO[OL] [file_name[.ext] REP[LACE]`。

**APP[END]**

在已有文件后追加输出结果内容，此时file_name[.ext]需指定为已有文件的名称。

**OFF**

停止保存输出内容到文件中。

在yasql会话中使用SPOOL命令的流程通常如下：

1. 执行`SPO[OL] file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]`命令开启保存为文件。
2. 执行其他SQL语句（例如SELECT语句等），所有输出结果都会被保存到指定文件中。
3. 执行`SPO[OL] OFF`命令停止保存。

若执行`SPO[OL] file_name[.ext] [CRE[ATE] | REP[LACE] | APP[END]`后再重复执行该命令，效果等同于在两次SPOOL命令之间执行过`SPO[OL] OFF`命令。

示例

```sql
spool employees.txt create
SELECT * FROM employees;
spool off
```

执行上述示例后，会创建一个名为employees.txt的文件并将查询结果将会保存到该文件中。

### 执行PL语句

登录数据库后，在yasql命令行界面可直接执行PL语句，执行PL语句时需要在过程体后加“/”。

示例

```sql
SET serveroutput ON
  
DECLARE
i INT;
BEGIN
i := 0;
IF i < 5 THEN
i := i + 1;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END IF;
END;
  /
 
This is: 1
```

### 执行shell命令

在yasql命令行界面，通过`!`可以执行操作系统层面的shell命令。

示例

```sql
-- 在yasql中执行外部OS命令date 
!date
Tue Aug  3 14:11:39 CST 2021
```

### 终止执行SQL操作

终止当前执行的SQL进程有以下方法：

- 在yasql命令行界面按**Ctrl**+**C**，如果当前存在正在执行的SQL语句，yasql会发送cancel消息到服务端，由服务端终止当前SQL。

- yasql登录的进程在操作系统层面被终止时，直接退出。

### 查看参数信息

- 通过show TIMI\[NG\] | AUTO\[COMMIT\] | SERVEROUT\[PUT\] | DIRE\[CTEXECUTE\] | NUM\[WIDTH\] |FEED\[BACK\] | HEA\[DING\]可以查看yasql的参数信息。

- 通过show parameter param_name可以查看YashanDB的配置参数信息。

示例

```sql
SHOW SERVEROUT
serveroutput OFF
 
SHOW PARAMETER DB_BLOCK_SIZE;
NAME                   VALUE    
---------------------- ----------
DB_BLOCK_SIZE          8192
```

### 设置显示宽度

在输出浮点类型和NUMBER类型数据时，可通过set num\[width\] \<1~128>命令设置显示宽度，该宽度默认为10，取值范围为\[1,128\]。

示例

```sql
show num
numwidth 10

CREATE TABLE item(ino INT, price DOUBLE);
 
 
INSERT INTO item VALUES(1, 57.6783924);
 
 
SELECT INO,PRICE FROM item;
 
         INO       PRICE
------------ -----------
           1  5.768E+001
 
 
SET num 20
SELECT INO,PRICE FROM item;
 
         INO                 PRICE
------------ ---------------------
           1       5.76783924E+001
 
```

### 事务自动提交

通过set auto\[commit\] on|off命令可以设置是否开启自动提交，默认为off。当autocommit为on时，每执行完一条SQL语句都将自动提交事务，可使用show autocommit查看当前配置。

示例

```sql
SET autocommit ON
SET autocommit OFF
SET auto ON
SET auto OFF

show autocommit
autocommit OFF
```

### 获取执行耗时

通过set timi\[ng\] on|off可以设置是否开启执行耗时统计，默认为off，耗时显示格式为"Elapsed: hh:mm:ss.ff"。

示例

```sql
SET timing ON
SELECT open_mode FROM V$DATABASE;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
 
Elapsed: 00:00:00.000

SET timing OFF
SELECT open_mode FROM V$DATABASE;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
```

### 设置一次执行

通过set DIRE\[CTEXECUTE\] on|off可以设置是否开启SQL语句的一次执行，默认为on。

*   on：一条SQL语句执行一次报文交互。

*   off：一条SQL语句执行两次报文交互。

示例

```sql
SET directexecute ON
SET directexecute OFF

show directexecute
directexecute OFF
```

### 替换占位符

通过set DEF[INE] ON或OFF可以设置是否开启扫描并替换命令中的占位符“&”变量，默认为on。可使用show DEF[INE]查看当前配置。

- on：替换占位符。

- off：不替换占位符。

示例

```sql
SET define ON
SET define OFF

show define
define OFF
```

DEFINE开关为ON时，yasql会扫描并替换命令中的占位符“&”变量。

若提前通过DEFINE为变量赋值，则不会出现提示，否则yasql会提示需要输入相应的值赋给变量。

示例

```sql
SELECT '&yasql' FROM dual;
Enter value for yasql: YashanDB
old     1: SELECT '&yasql' FROM dual
new     1: SELECT 'YashanDB' FROM dual

'YASHANDB'
----------
YashanDB

```

### 变量赋值

通过DEFINE指令为变量赋值（CHAR值），然后在SQL语句中使用该变量。

输入不带子句的DEFINE以列出所有替换变量的值和类型。

如果为变量赋值了数字20，yasql会指定一个CHAR值，该值由2和0两个字符组成。

若提前通过DEFINE为变量赋值，则不会出现提示，否则yasql会提示需要输入相应的值赋给变量。

示例

```sql
-- 定义变量
DEFINE POS = MANAGER
DEFINE DEPARTMENT_ID = 20
-- 查询变量
DEFINE
DEFINE DEPARTMENT_ID           = "20" (CHAR)
DEFINE POS             = "MANAGER" (CHAR)
-- 使用变量
SELECT '&POS' FROM dual;
old     1: SELECT '&POS' FROM dual
new     1: SELECT 'MANAGER' FROM dual

'MANAGER'
---------
MANAGER


SELECT '&DEPARTMENT_ID' FROM dual;
old     1: SELECT '&DEPARTMENT_ID' FROM dual
new     1: SELECT '20' FROM dual

'20'
-----
20

```

### 显示反馈信息

通过set FEED\[BACK\] on|off可以设置是否显示反馈信息，默认为on。

- on：显示反馈信息。

- off：不显示反馈信息。

反馈信息可能为“Succeed.”、“PL/SQL Succeed.”或“ROW_NUM row/rows affected.”，不包含错误码。

示例

```sql
SET feedback ON
SELECT * FROM dual;
 
DUMMY
-----
X
 
 
SET feedback OFF
SELECT * FROM dual;
DUMMY
-----
X

show feedback
feedback OFF
```

### 打印列标题

通过set HEA\[DING\] on|off可以设置是否打印显示报告中的列标题，默认为on。

- on：打印列标题。

- off：不打印列标题。SET HEADING OFF不会影响显示的列宽。

示例

```sql
SET heading ON
SELECT * FROM dual;
 
DUMMY
-----
X
 
 
SET heading OFF
SELECT * FROM dual;
 
X
 
 
show heading
heading OFF
```

### 列显示属性

#### 指定列显示属性

指定目标列的显示属性。通过列名确定目标，如果从不同的表中查询具有相同名称的列，则该列名的COLUMN命令将应用于所有具有相同名称的列。


命令格式：

```sql
-- 格式1
COL/COLUMN <column name> FOR/FORMAT <format string>

-- 格式2
COL/COLUMN "<column name>" FOR/FORMAT "<format string>"

-- 格式3
COL/COLUMN '<column name>' FOR/FORMAT '<format string>'
```

- COLUMN命令大小写不敏感，无通配符。

- 通常使用单/双引号包围或无引号均可，但当其值存在特殊符号时则必须使用引号包围。

- 如需在COLUMN命令中使用表达式，必须与SELECT命令中的表达式完全相同。例如SELECT命令中的表达式为a+b，COLUMN命令中则必须为a+b，不得改为b+a、（a+b）或其他。

- "\<column name>"长度范围为[1,64]个字节。

指定列显示属性的使用规则如下：

- 目前仅支持对字符列使用COLUMN命令，且其默认宽度为数据库中列的宽度，如需将调整显示宽度需使用FORMAT An，其中n取[1,32000]区间的整数。当指定的宽度小于字符串长度时，默认直接截断（truncate）列中的元素进行显示。

- 若对数值列使用COLUMN命令，该列将会显示为“#”字符串。

示例

```sql
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          不分部门
010          销售部
008          采购部
002          财务部


col department_name format a10;
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          不分部门
010          销售部
008          采购部
002          财务部


col department_name format a6;
SELECT deparment_no,department_name FROM department;

DEPARMENT_NO DEPA
------------ ----
000          不分
010          销售
008          采购
002          财务

```

#### 查看列显示属性

- 通过COL/COLUMN \<column name>命令可以查看指定单列的当前显示属性。

- 通过COL/COLUMN命令可以查看所有列的当前显示属性。

示例

```sql
-- 显示单列的显示属性值
col col1
COLUMN	 col1 ON
FORMAT	 a11

-- 显示所有列的显示属性值
col
COLUMN	 col1 ON
FORMAT	 a11
```

#### 重置/清除列显示属性

- 通过COLUMN命令的CLEAR子句可以重置单列的显示属性。

- 通过CL/CLEAR COL/COLUMN/COLUMNS可以将COLUMN命令设置的列显示属性重置为所有列的默认设置。
示例

```sql
-- 将单列的显示属性值清除
col col2 clear
col
COLUMN	 col1 ON
FORMAT	 a11

-- 将所有列的显示属性值清除
CLEAR COLUMNS
columns cleared
```

### 获取描述信息

可以通过describe或desc \<table name>|\<view name>命令获取数据库对象的描述信息。

示例

```sql
desc v$database
NAME                                                             NULL?     DATATYPE                          
---------------------------------------------------------------- --------- --------------------------------- 
DATABASE_ID                                                                BIGINT                            
DATABASE_NAME                                                              VARCHAR(64)                       
CREATE_TIME                                                                DATE                              
LOG_MODE                                                                   VARCHAR(16)                       
OPEN_MODE                                                                  VARCHAR(16)                       
PROTECTION_MODE                                                            VARCHAR(32)                       
PROTECTION_LEVEL                                                           VARCHAR(32)                       
DATABASE_ROLE                                                              VARCHAR(16)                       
BLOCK_SIZE                                                                 INTEGER                           
CURRENT_SCN                                                                BIGINT                            
STATUS                                                                     VARCHAR(32)                       
RCY_POINT                                                                  VARCHAR(32)                       
FLUSH_POINT                                                                VARCHAR(32)                       
RESET_POINT                                                                VARCHAR(32)                       
PLATFORM_NAME                                                              VARCHAR(256)                      
HOST_NAME                                                                  VARCHAR(256)                      
RESTORE_TIME                                                               DATE                              
SWITCHOVER_STATUS                                                          VARCHAR(32)                       
SUPPLEMENTAL_LOG_DATA_MIN                                                  VARCHAR(8)                        
SUPPLEMENTAL_LOG_DATA_PK                                                   VARCHAR(8)                        
SUPPLEMENTAL_LOG_DATA_ALL                                                  VARCHAR(8)                        
SUPPLEMENTAL_LOG_TABLE_TYPE                                                VARCHAR(16)                        
```

### 退出登录

#### 手动退出

通过EXIT [SUCCESS | FAILURE | WARNING | n ] [COMMIT | ROLLBACK]命令可退出yasql，并执行相应操作。

EXIT能够指定操作系统返回代码。关键字SUCCESS、WARNING和FAILURE表示与操作系统相关的值。在Linux上，返回代码只有一个字节的存储空间；因此，操作系统返回代码的范围受到限制，返回代码的范围被限制为0到255。

执行EXIT默认为执行EXIT SUCCESS COMMIT。如果在EXIT选项中出现语法错误或使用非数字变量，yasql将执行EXIT FAILURE COMMIT。

```sql
-- 直接退出
EXIT

-- 执行回滚操作后退出，并返回99
EXIT 99 rollback

-- 提交所有未提交的事务后退出，并返回最后执行的SQL命令或PL块的错误代码
EXIT SQL.SQLCODE

-- 以成功状态退出，并返回值为0表示成功
EXIT SUCCESS

-- 以失败状态退出，并返回值为1表示失败
EXIT FAILURE

-- 以警告状态退出，并返回值为2表示警告
EXIT WARNING
```

#### 报错自动退出

通过WHENEVER SQLERROR EXIT [SUCCESS | FAILURE | WARNING | n]  [COMMIT | ROLLBACK]命令可以启动报错自动退出，即当SQL命令或PL块生成错误时执行相应的EXIT操作，此处的EXIT语法与主动退出的EXIT语句完全一致。

> **Note**:
> 
> 仅在错误码为YAS的错误场景生效，且仅在当前yasql进程生效，在错误码为YASQL的错误场景不生效。开启set serveroutput on后，匿名块中的error occurred不生效。

```sql
-- 遇到报错后直接退出
WHENEVER SQLERROR EXIT
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing

-- 遇到报错后执行回滚操作后再退出，并返回99
WHENEVER SQLERROR EXIT 99 rollback
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing

-- 遇到报错后提交所有未提交的事务后再退出，并返回最后执行的SQL命令或PL块的错误代码
WHENEVER SQLERROR EXIT SQL.SQLCODE
INSERT test;

[1:8]YAS-04115 "INTO" expected but missing
```

### 断开连接

使用disconnect命令或disc命令断开与数据库的连接，断开连接后将无法执行SQL语句，可通过conn命令重新登录。

```sql
disc
```
