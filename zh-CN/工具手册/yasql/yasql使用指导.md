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
$ yasql [-S] username/password@host:port[/pdb_name] [as role_name] [-c "SQL" | -f [-e]  filename[.ext] | @filename[.ext]] 
SQL> <yasql_subcommand>
```

**-S**

指定[静默登录](#login)，需确保-S为首个参数，该参数大小写不敏感。

**username/password**或**/**

指定[登录或连接](#login)数据库的数据库用户名和密码。

**@host:port[/pdb_name]**

指定需[登录与连接](#login)的目标数据库的服务端地址，其中`/pdb_name`仅用于容器数据库指定连接到具体某个PDB（省略则默认连接至根容器）。  

<span id="role_name" name="role_name"></span>

**as role_name**

声明用户[登录](#login)时采用的角色，可选项包括系统超级管理员SYSDBA、管理员DBA、审计管理员AUDIT_ADMIN、安全管理员SECURITY_ADMIN以及普通用户NORMAL，其中NORMAL指定与否均无影响。角色名称大小写不敏感。

**-c "SQL"**

用于登录并执行单条SQL语句（执行完后立即退出），详情请查阅[执行SQL语句](#singlesql)。

**-f [-e] filename[.ext]**

用于执行SQL文件，详情请查阅[执行SQL文件](#fsql)。

**@filename[.ext]**

用于执行SQL文件，详情请查阅[执行SQL语句](#sqlfile)。

**yasql_subcommand**

通过yasql连接登录数据库后可以执行的子命令（不区分大小写），具体命令清单如下表所示。

|  子命令| 功能|
| ------------ | ------------------------------------------ |
| [@filename](#sqlfile)<br />[@@filename](#sqlfile) | 执行SQL文件 |
| [!{OS_command}](#shell) | 执行操作系统层面的shell命令 |
| [set lastlogin on](#lastlogin) | 设置是否展示该用户的上次登录记录 |
| [show](#show) | 查看参数配置 |
| [desc](#desc) | 获取数据库对象的描述信息，例如表/视图的字段定义                                     |
| [set DIRECTEXECUTE](#directexecute) | 设置是否开启SQL语句的一次执行 |
| [set AUTOCOMMIT](#autocommit) | 设置是否开启自动提交事务 |
| [set TIMING](#timing) | 设置是否开启执行耗时统计 |
| [set FEEDBACK](#feedback) | 设置是否显示反馈信息，例如“ROW_NUM row/rows affected.” |
| [set SERVEROUTPUT](#serveroutput) | 设置是否打印服务端[DBMS_OUTPUT](../../开发手册/PL参考手册/内置高级包/DBMS_OUTPUT).PUT_LINE设置的缓冲区的信息 |
| [spool](#spool) | 将SQL语句的输出保存为文件 |
| [set AUTOTRACE](#autotrace) | 设置是否生成DML语句（SELECT/INSERT/UPDATE/DELETE）的执行计划分析报告 |
| [set nchar_literal_replace](#nchar) | 设置是否开启NCHAR字面量替换 |
| [set DEFINE](#set_def) | 设置是否开启扫描并替换命令中的占位符“&”变量 |
| [DEFINE](#DEFINE) | 为变量赋值（CHAR值） |
| [set NUMWIDTH](#numwidth) | 设置浮点类型和NUMBER类型数据输出的显示宽度 |
| [set HEADING](#heading) | 设置是否打印查询结果中的列标题 |
| [COLUMN](#COLUMN) | 管理列的显示属性 |
| [EXIT](#EXIT) | 退出登录 |

## 功能介绍

<span id="login" name="login"></span>

### 登录与连接

如需登录YashanDB，首先需要获取数据库的认证信息和连接信息，然后选择合适的登录方式进行操作。

#### 认证信息

认证信息是指用于登录YashanDB的用户名和认证凭据。

|  格式| 说明|
|--------------------|-----------------------|
| username/password <br> /| 连接并登录数据库的用户名和密码，通过[操作系统认证](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)登录时可免密（即省略用户名和密码），但不能省略`/`。<br/>如果在yashan模式下创建用户时用户名未使用双引号包围，使用yasql连接时大小写不敏感。<br/>如果用户名或密码中含有OS命令相关的特殊字符（例如`@`、`/`、`.`、`!`、`$`、`'`等）需进行转义改写。  |
| as role_name | 声明用户登录时采用的角色，可选项包括系统管理员`SYSDBA`、管理员`DBA`、安全管理员`SECURITY_ADMIN`、审计管理员`AUDIT_ADMIN`以及普通用户`NORMAL`，其中`NORMAL`声明与否均无影响。角色名称大小写不敏感。<br/><br/>在以下场景中，登录时必须声明角色且用户与角色匹配才能登录成功：<br/>* 采用[操作系统认证](../../产品安全/身份标识与鉴别/操作系统认证/00操作系统认证.md)登录时，必须声明角色为SYSDBA。<br/>* 开启[三权分立](../../产品安全/数据访问控制/三权分立.md)且强制要求管理员用户登录时声明角色（默认不强制，如需启用该功能请联系我们的技术支持）时，各类管理员登录时必须声明其相应的角色。 |

用户名或密码中含有特殊符号（下划线除外）时，处理建议如下：

- [创建](../../开发手册/SQL参考手册/SQL语句/CREATE USER)/[管理](../../开发手册/SQL参考手册/SQL语句/ALTER USER)用户等操作中指定相应值时需使用双引号包围。

- 登录数据库时，为确保yasql能正确向数据库服务端转递用户名和密码的值，建议[分步登录](#nolog)，此类用户名或密码只需使用双引号包围即可，若直接登录则需对其进行相应改写：

    - 如果用户名或密码中本身不存在单引号，直接使用单引号包围即可进行传递，例如`user@name`、`password$`需转义改写为`'"user@name"'/'"password$"'`。

    - 如果用户名或密码中本身已存在单引号，则无法再使用单引号进行传递，需酌情使用转义符和双引号：

        - 如果同时存在单引号和Linux系统等环境下的功能符号，例如变量替换符号、变量替换符、转义符等，即`\`、`$`、`"`、`'`，需使用多重转义，例如`user'1$name`、`pass'1\word`需转义改写为`"\"user'1\$name\""/"\"pass'1\\word\""`。

        - 如果存在单引号且不存在上述功能符号，则需要叠加使用双引号，例如`user'name`、`password`需转义改写为`"\"user'name\""/password`。

> **Note**: 
>
> 为正确将用户名传递至数据库服务端，使用yasql以mysql模式下创建的用户连接YashanDB服务端（mysql模式）时需额外使用双引号将用户名包围。

### 服务端地址

服务端地址可以为URL或别名，需正确且唯一指向目标服务端的监听地址（即yasboot cluster status命令回显结果中的listen_address）。

|  信息类型| 格式| 说明|
|--------------------|-----------------------|---------------|
| URL | @host:port[/pdb_name] | * host：数据库所在服务器的网络地址，可以为IPv4地址、IPv6地址或域名。在共享集群部署中，若已配置[SCAN](../../数据库管理/集群管理/SCAN管理.md)或[VIP](../../数据库管理/集群管理/VIP管理.md)，还可以使用相应的域名或IP地址。<br/>* port：数据库服务端监听端口，如安装过程中未进行调整，默认为1688。<br/>* pdb_name：仅用于容器数据库，指定连接到具体某个PDB，省略则默认连接至根容器。 |
| 无URL|  | 采用[UDS本地连接](../../产品安全/连接管理/00连接管理.md)访问$YASDB_DATA环境变量对应的数据库实例。在容器数据库中，该方式只能连接至根容器。<br/>使用该方式连接数据库属于高权限操作，请合理使用。 |
| 别名 | @别名 | 使用自定义的别名连接数据库。别名需提前在客户端所在设备的$YASDB_HOME/client/yasc_service.ini文件中定义，格式为`alias = host:port[/pdb_name]`。例如别名定义为`yashan = 192.168.1.2:1688`则可直接使用`@yashan`连接该地址所指向的数据库。 |

- 在容器数据库中，指定pdb_name连接指定PDB时，需使用配套的v23.5.1.100及以上版本的yasql。  

- 若使用IPv6地址连接，IP必须使用中括号包围，链路本地IPv6地址还需使用百分号标识网络接口名。系统网卡需要支持IPv6的连接，目前支持：  

  - IPv6环回地址，例如`@[::1]:1688`

  - 链路本地地址，例如`@[fe80::a89b:8fdb:2c28:35ce%ens192]:1688`

  - 唯一本地地址，例如`@[fc00:7::126]:1688`

  - 未指定地址，例如`@[::]:1688`

  - 全球单播地址，例如`@[2001:250:4000:4000:f80f:6d4c:4310:35ce]:1688`

  - IPv4映射的IPv6地址，例如`@[::ffff:192.168.7.126]:1688`

- 若使用域名连接，例如`@yashandb.vip:1688`，域名需能正确解析到实际的数据库IP地址。若域名解析到多个IP地址，则将按照解析顺序依次尝试连接这些IP地址直至成功。

#### 登录方式 

登录方式可分为以下几种：
<span id="nolog" name="nolog"></span>

|  登录方式|  说明|
|--------------------|-----------------------|
| 直接登录 | 命令格式：`yasql username/password@host:port[/pdb_name]`  | 
| 静默登录 | 命令格式：`yasql -S username/password@host:port[/pdb_name]` <br/>使用该方式登录并连接数据库不返回命令行提示、命令回显信息、登录提示信息和版本信息。 |
| 操作系统认证登录 | 命令格式：`yasql / as sysdba`或`yasql username/password as sysdba`  <br/>使用该方式登录无需配置连接方式，只能通过UDS本地连接登录到$YASDB_DATA环境变量所对应的数据库实例。 |
| 分步登录|  1. 使用/nolog命令启动yasql，命令格式为`yasql /nolog`，此时仅打开登录窗口但不连接数据库。<br/>2. 使用connect或conn命令连接并登录数据库，命令格式为`connect username/password@host:port`或`conn username/password@host:port[/pdb_name]`。|
| 切换登录 | 登录后使用`connect`或`conn`命令切换至其它用户登录。

#### 登录示例 

```shell
# 直接登录+有URL的IPv4连接方式
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# 直接登录+有URL的IPv6环回地址连接方式
$ yasql sales/********@[::1]:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# 静默登录+有URL的域名连接方式
$ yasql -S sales/sales@123.com:1688

# 操作系统认证登录
$ yasql / as sysdba
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# 直接登录+别名连接
$ cat ${YASDB_HOME}/client/yasc_service.ini
yashan = 127.0.0.1:1688
$ yasql sales/********@yashan
Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>

# 分步登录
$ yasql /nolog
YashanDB SQL Enterprise Edition Release {version_number} x86_64
SQL> conn sales/sales@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL>
-- 登录成功

-- 切换登录：通过用户密码+无URL的连接方式切换至另一个用户
SQL> conn sales1/sales1

-- 切换登录：通过用户密码+有URL的IPv4连接方式切换至另一个用户
SQL> conn sales1/sales1@192.168.1.3:1688
SQL>
```

### 执行SQL语句

**方式一**：登录后执行SQL语句

登录数据库后，在yasql命令行界面可直接执行SQL语句。

示例

```shell
$ yasql sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
SQL> CREATE TABLE customer(cno INT, cname VARCHAR(50));
 
Succeed.
 
SQL> INSERT INTO customer VALUES(1, 'customer1');
 
1 row affected.
 
SQL> SELECT cno,cname FROM customer;
 
         CNO CNAME                                                
------------ -----------------------------------------------------
           1 customer1                                           
 
1 row fetched.
```

<span id="singlesql" name="singlesql"></span>
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

<span id="fsql" name="fsql"></span>
**方式一**：通过-f\[-e\]参数

通过参数-f \[-e\] filename[.ext]可以直接在操作系统层面连接登录到数据库执行一个SQL文件，后缀名可省略。

- -f用于指定需执行的SQL文件。
- -e用于显示文件中被执行的具体语句。

当SQL文件位于yasql工具同目录时无需指定文件路径，否则需指定文件所在路径（绝对路径或相对路径均可）。

示例

```sql
-- s.sql在yasql同目录下
$ yasql sales/******** -f s.sql
SQL> select 1 from dual;
 
           1
------------
           1
 
1 row fetched.
 
SQL> select 2 from dual;
 
           2
------------
           2
 
1 row fetched.
```

<span id="sqlfile" name="sqlfile"></span>
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
 
1 row fetched.

           2
------------
           2
 
1 row fetched.

-- 绝对路径
@/home/yasdb/s.sql
 
           1
------------
           1
 
1 row fetched.

           2
------------
           2
 
1 row fetched.
 
-- 相对路径
@../s.sql
 
           1
------------
           1
 
1 row fetched.

           2
------------
           2
 
1 row fetched.
```

### 执行PL语句

登录数据库后，在yasql命令行界面可直接执行PL语句，执行PL语句时需要在过程体后加“/”。

示例

```sql
set serveroutput on
  
DECLARE
i int;
BEGIN
i := 0;
IF i < 5 THEN
i := i + 1;
DBMS_OUTPUT.PUT_LINE ('This is: '||i);
END IF;
END;
  /
 
This is: 1
PL/SQL Succeed.
```

<span id="shell" name="shell"></span>

### 执行shell命令

在yasql命令行界面，通过`!`可以执行操作系统层面的shell命令。

示例

```sql
-- 在yasql中执行外部OS命令date 
!date
Tue Aug  3 14:11:39 CST 2021
```

<span id="lastlogin" name="lastlogin"></span>

### 设置展示上次登录记录

在[开启审计功能](../../产品安全/安全审计/安全审计基础配置.md)且[创建并使能](../../产品安全/安全审计/审计策略管理.md)了包含系统行为LOGON的审计策略的前提下，在使用yasql登录YashanDB时，可通过开启客户端的LASTLOGININFO属性，展示用户的上次登录记录。记录信息包括：

- 若此次登录成功，将展示上次登录的记录：

  - 上次登录成功：展示日期、时间、客户端主机名或IP等。
  
  - 上次登录失败：展示日期、时间、访问应用程序、IP 地址和访问方法（客户端的类型、连接方式）、失败的次数。

  - 不存在上次登录（即此次为首次登录）：无信息。

- 若此次登录失败，则无信息。

开启客户端的LASTLOGININFO属性方法如下：

- 永久生效：通过yasql.ini配置文件进行配置。

  1. 查找YashanDB客户端文件夹下是否存在client/yasql.ini文件，若无则创建。

  2. 在yasql.ini文件添加以下配置信息并保存。

    ```ini
    LASTLOGININFO = on
    ```

- 当前窗口生效：打开yasql命令窗后，通过set lastlogin on|off命令设置，默认为off。

示例

```sql
-- 开启审计功能、准备审计策略

ALTER SYSTEM SET UNIFIED_AUDITING=TRUE;
CREATE AUDIT POLICY logon ACTIONS LOGON;
AUDIT POLICY logon;

set lastlogin on
conn sales/********

Last Successful login time: 2025-02-28 15:25:31.235728 Program: yasql Client message: (TYPE=(DATABASE));(CLIENT ADDRESS=((PROTOCOL=uds)(HOST=localhost)(PORT=0)))

conn sales1/********

Last Failed login time: 2025-02-28 15:17:57.240383 and the last 3 consecutive login attempts failed, Program: yasql Client message: (TYPE=(DATABASE));(CLIENT ADDRESS=((PROTOCOL=uds)(HOST=localhost)(PORT=0)))
```

### 终止执行SQL操作

终止当前执行的SQL进程有以下方法：

- 在yasql命令行界面按**Ctrl**+**C**，如果当前存在正在执行的SQL语句，yasql会发送cancel消息到服务端，由服务端终止当前SQL。
- yasql登录的进程在操作系统层面被终止时，直接退出。

<span id="show" name="show"></span>

### 查看参数信息

- 通过show parameter param\_name可以查看YashanDB的[配置参数](../../参考手册/配置参数.md)信息。
- 通过show param\_name可以查看yasql的参数信息（通过set命令进行配置的参数），例如[NUM\[WIDTH\]](#numwidth)、[AUTO\[COMMIT\]](#autocommit)等。

示例

```sql 
SHOW PARAMETER DB_BLOCK_SIZE;
NAME                   VALUE    
---------------------- ----------
DB_BLOCK_SIZE          8192

SHOW SERVEROUT
serveroutput OFF
```
<span id="show_pdbs" name="show_pdbs"></span>

### 查看PDB信息


在容器数据库中，通过show pdbs命令可以查看PBD信息，包括容器ID、PDB名称以及运行状态。  

- 连接根容器执行该命令，查询当前环境中所有PDB（含种子容器）的信息。  

- 直连某个PDB执行该命令，查询当前PDB的信息。  

示例（单机/共享集群/分布式集群部署）

```shell
# 连接根容器执行该命令
$ yasql c##sales/********@192.168.1.2:1688
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    1 PDB$SEED                                                         CLOSED
                    2 PDB1                                                             OPEN

SQL> exit

# 直连PDB执行该命令
$ yasql sales/********@192.168.1.2:1688/pdb1
YashanDB SQL Enterprise Edition Release {version_number} x86_64

Connected to:
YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

SQL> show pdbs

               CON_ID CON_NAME                                                         STATUS
--------------------- ---------------------------------------------------------------- -----------------
                    2 PDB1                                                             OPEN
```


<span id="desc" name="desc"></span>

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

<span id="directexecute" name="directexecute"></span>

### 设置一次执行

通过set DIRE\[CTEXECUTE\] on|off可以设置是否开启SQL语句的一次执行，默认为on。

*   on：一条SQL语句执行一次报文交互。

*   off：一条SQL语句执行两次报文交互。

示例

```sql
set directexecute on
set directexecute off

show directexecute
directexecute OFF
```

<span id="autocommit" name="autocommit"></span>

### 设置事务自动提交

通过set auto\[commit\] on|off命令可以设置是否开启自动提交，默认为off。当autocommit为on时，每执行完一条SQL语句都将自动提交事务，可使用show autocommit查看当前配置。

示例

```sql
set autocommit on
set autocommit off
set auto on
set auto off

show autocommit
autocommit OFF
```

<span id="timing" name="timing"></span>

### 设置执行耗时统计

通过set timi\[ng\] on|off可以设置是否开启执行耗时统计，默认为off，耗时显示格式为"Elapsed: hh:mm:ss.ff"。

示例

```sql
set timing on
SELECT open_mode FROM v$database;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
1 row fetched.
 
Elapsed: 00:00:00.000

set timing off
SELECT open_mode FROM v$database;
 
OPEN_MODE        
-----------------
READ_WRITE      
 
1 row fetched.
```

<span id="feedback" name="feedback"></span>

### 设置显示反馈信息

通过set FEED\[BACK\] on|off可以设置是否显示反馈信息，默认为on。

- on：显示反馈信息。
- off：不显示反馈信息。

反馈信息可能为“Succeed.”、“PL/SQL Succeed.”或“ROW_NUM row/rows affected.”，不包含错误码。

示例

```sql
set feedback on
select * from dual;
 
DUMMY
-----
X
 
1 row fetched.
 
set feedback off
select * from dual;
DUMMY
-----
X

show feedback
feedback OFF
```

<span id="serveroutput" name="serveroutput"></span>

### 设置打印缓冲区信息

通过set SERVEROUT[PUT] on|off可以设置是否打印服务端[DBMS_OUTPUT](../../开发手册/PL参考手册/内置高级包/DBMS_OUTPUT).PUT_LINE设置的缓冲区的信息，默认为OFF。

- on：每执行一条SQL语句或调用匿名PL后会调用DBMS_OUTPUT.GET_LINES()获取缓冲区内剩余的信息并打印。
- off：关闭打印开关。

示例（单机、共享集群部署）

```plsql
set serveroutput on;
BEGIN
    DBMS_OUTPUT.ENABLE(3000);
    DBMS_OUTPUT.PUT('yashanDB');
    DBMS_OUTPUT.PUT_LINE('hello world!');
    DBMS_OUTPUT.PUT('coming'); 
END;
/

--result
yashanDBhello world!

PL/SQL Succeed.

set serveroutput off
SQL> BEGIN
   2     DBMS_OUTPUT.ENABLE(3000);
   3     DBMS_OUTPUT.PUT('yashanDB');
   4     DBMS_OUTPUT.PUT_LINE('hello world!');
   5     DBMS_OUTPUT.PUT('coming');
   6 END;
   7 /

PL/SQL Succeed.
```

<span id="spool" name="spool"></span>

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
select * from employees;
spool off
```

执行上述示例后，会创建一个名为employees.txt的文件并将查询结果将会保存到该文件中。

<span id="autotrace" name="autotrace"></span>

### 生成AUTOTRACE报告 

通过set AUTOTRACE命令设置是否生成DML语句（SELECT/INSERT/UPDATE/DELETE）的执行计划分析报告并指定具体需返回哪些信息块，报告的详细介绍请查阅[AUTOTRACE](../../性能调优/性能调优特性与工具/AUTOTRACE.md)。

命令格式：

```sql
SET AUTOT[RACE] ON|TRACE[ONLY]|OFF [EXP[LAIN]] [STAT[ISTICS]]
```

- ON：输出SQL语句的执行结果和执行计划分析报告，且报告包含执行计划和执行统计信息，等同于ON EXP[LAIN] STAT[ISTICS]

  - ON EXP[LAIN]：输出SQL语句的执行结果和执行计划。

  - ON STAT[ISTICS]：输出SQL语句的执行结果和执行统计信息。

- TRACE[ONLY] [EXP[LAIN]] [STAT[ISTICS]]：仅生成执行计划分析报告（报告中包含的信息块同上），不输出SQL语句的执行结果。

- OFF（默认值）：不生成报告。

> **Note**:
>
> 统计信息仅在STATISTICS_LEVEL参数设置为TYPICAL或ALL时记录。

<span id="nchar" name="nchar"></span>

### 设置NCHAR字面量替换

通过set nchar\[_literal_replace\] true|false可以设置是否开启NCHAR字面量替换，默认为false。

示例

```sql
SELECT N'12%',
TYPEOF(N'12%') TYPE
FROM DUAL;
N'12%'            TYPE
----------------- ---------
12%               nchar

set nchar_literal_replace true
SELECT N'12%',
TYPEOF(N'12%') TYPE
FROM DUAL;
U'\0031\0032\0025' TYPE
------------------ ---------
12%                nchar
```

<span id="set_def" name="set_def"></span>

### 设置替换占位符

通过set DEF[INE] ON或OFF可以设置是否开启扫描并替换命令中的占位符“&”变量，默认为on。可使用show DEF[INE]查看当前配置。

- on：替换占位符。
- off：不替换占位符。

示例

```sql
set define on
set define off

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

1 row fetched.
```

也可以通过ACC[EPT] variable [CHAR] [PROMPT text|NOPR[OMPT]] [HIDE]语法来为变量赋值。

示例

```sql
ACCEPT username CHAR PROMPT '请输入用户名：';
yashan
ACCEPT password CHAR PROMPT '请输入密码：'  HIDE;
yashan
conn &username/&password
```

<span id="DEFINE" name="DEFINE"></span>

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

1 row fetched.

SELECT '&DEPARTMENT_ID' FROM dual;
old     1: SELECT '&DEPARTMENT_ID' FROM dual
new     1: SELECT '20' FROM dual

'20'
-----
20

1 row fetched.
```

<span id="numwidth" name="numwidth"></span>

### 设置显示宽度

在输出浮点类型和NUMBER类型数据时，可通过set num\[width\] <1~128>命令设置显示宽度，该宽度默认为10，取值范围为\[1,128\]。

示例

```sql
show num
numwidth 10

CREATE TABLE item(ino INT, price DOUBLE);
 
Succeed.
 
INSERT INTO item VALUES(1, 57.6783924);
 
1 row affected.
 
SELECT INO,PRICE FROM item;
 
         INO       PRICE
------------ -----------
           1  5.768E+001
 
1 row fetched.
 
set num 20
SELECT INO,PRICE FROM item;
 
         INO                 PRICE
------------ ---------------------
           1       5.76783924E+001
 
1 row fetched.
```

<span id="heading" name="heading"></span>

### 设置打印列标题

通过set HEA\[DING\] on|off可以设置是否打印查询结果中的列标题，默认为on。

- on：打印列标题。
- off：不打印列标题。SET HEADING OFF不会影响显示的列宽。

示例

```sql
set heading on
select * from dual;
 
DUMMY
-----
X
 
1 row fetched.
 
set heading off
select * from dual;
 
X
 
1 row fetched.
 
show heading
heading OFF
```


<span id="COLUMN" name="COLUMN"></span>

### 管理列显示属性

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
- "<column name>"长度范围为[1,64]个字节。

指定列显示属性的使用规则如下：

- 目前仅支持对字符列使用COLUMN命令，且其默认宽度为数据库中列的宽度，如需将调整显示宽度需使用FORMAT An，其中n取[1,32000]区间的整数。当指定的宽度小于字符串长度时，默认直接截断（truncate）列中的元素进行显示。
- 若对数值列使用COLUMN命令，该列将会显示为“#”字符串。

示例

```sql
select deparment_no,department_name from department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

col department_name format a10;
select deparment_no,department_name from department;

DEPARMENT_NO DEPARTMENT_NAME
------------ ---------------------
000          Public Dep
010          Sales Dep
008          Purchasing Dep
002          Finance Dep

4 rows fetched.

col department_name format a6;
select deparment_no,department_name from department;

DEPARMENT_NO DEPA
------------ ----
000          不分
010          销售
008          采购
002          财务

4 rows fetched.
```

#### 查看列显示属性

- 通过COL/COLUMN <column name>命令可以查看指定单列的当前显示属性。
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

<span id="EXIT" name="EXIT"></span>

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
insert test;

[1:8]YAS-04115 "INTO" expected but missing

-- 遇到报错后执行回滚操作后再退出，并返回99
WHENEVER SQLERROR EXIT 99 rollback
insert test;

[1:8]YAS-04115 "INTO" expected but missing

-- 遇到报错后提交所有未提交的事务后再退出，并返回最后执行的SQL命令或PL块的错误代码
WHENEVER SQLERROR EXIT SQL.SQLCODE
insert test;

[1:8]YAS-04115 "INTO" expected but missing
```

### 断开连接

使用disconnect命令或disc命令断开与数据库的连接，断开连接后将无法执行SQL语句，可通过conn命令重新登录。

```sql
disc
```
