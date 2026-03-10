
对于从YashanDB到Oracle数据库的链接，系统存在如下前置要求：

- YashanDB服务端已安装dblink插件（可通过查看$YASDB_HOME/third路径下是否存在dblink文件夹确认）。

- YashanDB服务端已下载和安装Oracle Instant Client。

   未安装Oracle Instant Client的环境中，通过DBLINK向Oracle数据库发起远程链接，将会由于缺少必要的组件而抛出错误。

- YashanDB服务端已安装libaio库。

   YashanDB服务端缺少libaio库时，如通过DBLINK向Oracle数据库发起首次远程链接，yex_server沙箱进程可能在加载驱动时发生core dump（yasdb进程无影响）。本错误只在首次链接时影响yex_server进程，再次链接时系统可自动恢复正常。

鉴于上述要求，对于可能使用YashanDB -> Oracle数据库远程链接的数据库，管理员应按下述指导进行必要的操作。

## Oracle Instant Client下载和安装

1. 以YashanDB安装用户（例如yashan）登录到数据库服务器。

2. 根据YashanDB服务器环境，从[Oracle官网](https://www.oracle.com/database/technologies/instant-client/downloads.html)下载对应的Oracle Instant Client安装包。

   OCI版本要求如下：

    - x86：21系列

    - ARM：19系列

3. 将安装包解压到本地路径，例如`/home/oracle-instant-client/`。

4. 设置动态库依赖路径。

   ```shell
   $ vi ~/.bashrc

   # 在文件中添加如下信息并保存
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/oracle-instant-client/lib
   ```

5. 生效环境变量。

   ```shell
   $ source ~/.bashrc

   # 在回显信息中检查是否已存在上述路径
   $ echo $LD_LIBRARY_PATH
   ```

## libaio库安装

1. 以YashanDB安装用户（例如yashan）登录到数据库服务器。

2. 以Centos为例，通过以下命令安装libaio库：

   ```shell
   yum install libaio
   ```

## 创建dblink并进行操作

1. 以测试用户sales通过数据库客户端工具yasql连接数据库，数据库默认连接端口为1688。

   ```cmd
   > yasql sales/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> 
   ```

2. 创建DBLink。

   以下为示例，也可以参考[CREATE DATABASE LINK](../../SQL语句/CREATE DATABASE LINK)了解详细的DBLINK链接配置。

   ```sql
   -- YashanDB与YashanDB的数据库链接
   CREATE DATABASE LINK dblink_yashan CONNECT TO sales IDENTIFIED BY sales USING '192.168.1.2:1688';
   
   -- YashanDB与Oracle的数据库链接，且对所有用户可见
   CREATE PUBLIC DATABASE LINK dblink_oracle CONNECT TO REGRESS IDENTIFIED BY REGRESS USING 'oracle:192.168.1.2:1521/orcl';
   ```

3. 以DBLink链接到远端Oracle为例，进行数据操作。

   ```sql
   -- 以下为远端的Oracle上department表的表定义
   desc department;
   
   Name                                      Null?    Type
   ----------------------------------------- -------- ----------------------
   DEPARTMENT_ID                                      NUMBER
   FIRST_NAME                                         VARCHAR2(20)
      
   -- 在YashanDB上查询department表；
   SELECT * FROM department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   
   --在YashanDB上对远端department表插入数据；
   INSERT INTO department@dblink_oracle VALUES(00002,test);
   
   SELECT * FROM department@dblink_oracle;
   DEPARTMENT_ID           DEPARTMENT_NAME          
   ----------------------- ------------------------------
   00001                   develop
   00002                   test
   
   ```
   
   数据库服务可并发打开的DBLink连接由系统参数[DBLINK_CURSOR_COUNT](../../../../参考手册/配置参数.html#dblinkcursorcount)进行控制，参数默认值为32，可根据业务需求调整系统参数值。
   
   DBLink连接会申请系统空闲内存来缓存远端表业务数据，要求系统空闲内存 > DBLINK_CURSOR_COUNT * 513K，否则系统内存不足时再次打开dblink会产生报错YAS-00101。

4. 查看当前数据库服务上已创建的DBLink链接。

   ```sql
   SELECT * FROM DBA_DB_LINKS;
   ```

可通过[dblink语法说明](../../通用SQL语法/dblink/dblink语法说明)了解更多功能及约束。



## 常见故障处理

### YAS-07315 too many dblink result sets

当执行SQL语句访问dblink远端表时，如发生`YAS-07315 too many dblink result sets`错误提示，说明当前允许打开的dblink远端表数量已超过系统参数DBLINK_CURSOR_COUNT配置值。请在确认数据库服务器内存充足的情况下，适当调大参数值。

```sql
-- 1.查看系统参数配置
show parameter dblink_cursor_count

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
DBLINK_CURSOR_COUNT                                              32


-- 2.当打开的dblink远端表数量超过系统参数值时将发生错误提示
SELECT * FROM t1@dblink1 a1 CROSS JOIN t1@dblink1 a2 CROSS JOIN t1@dblink1 a3 CROSS JOIN t1@dblink1 a4 CROSS JOIN t1@dblink1 a5 CROSS JOIN t1@dblink1 a6 CROSS JOIN t1@dblink1 a7 CROSS JOIN t1@dblink1 a8 CROSS JOIN t1@dblink1 a9 CROSS JOIN t1@dblink1 a10 CROSS JOIN t1@dblink1 a11 CROSS JOIN t1@dblink1 a12 CROSS JOIN t1@dblink1 a13 CROSS JOIN t1@dblink1 a14 CROSS JOIN t1@dblink1 a15 CROSS JOIN t1@dblink1 a16 CROSS JOIN t1@dblink1 a17 CROSS JOIN t1@dblink1 a18 CROSS JOIN t1@dblink1 a19 CROSS JOIN t1@dblink1 a20 CROSS JOIN t1@dblink1 a21 CROSS JOIN t1@dblink1 a22 CROSS JOIN t1@dblink1 a23 CROSS JOIN t1@dblink1 a24 CROSS JOIN t1@dblink1 a25 CROSS JOIN t1@dblink1 a26 CROSS JOIN t1@dblink1 a27 CROSS JOIN t1@dblink1 a28 CROSS JOIN t1@dblink1 a29 CROSS JOIN t1@dblink1 a30 CROSS JOIN t1@dblink1 a31 CROSS JOIN t1@dblink1 a32 CROSS JOIN t1@dblink1 a33;

YAS-07315 too many dblink result sets

-- 3.修改系统参数
ALTER SYSTEM SET dblink_cursor_count=64 scope=spfile;


-- 4.重启数据库
-- 5.登录后查看参数值
show parameter dblink_cursor_count

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
DBLINK_CURSOR_COUNT                                              64


-- 6.重新执行业务SQL可正常执行
SELECT * FROM t1@dblink1 a1 CROSS JOIN t1@dblink1 a2 CROSS JOIN t1@dblink1 a3 CROSS JOIN t1@dblink1 a4 CROSS JOIN t1@dblink1 a5 CROSS JOIN t1@dblink1 a6 CROSS JOIN t1@dblink1 a7 CROSS JOIN t1@dblink1 a8 CROSS JOIN t1@dblink1 a9 CROSS JOIN t1@dblink1 a10 CROSS JOIN t1@dblink1 a11 CROSS JOIN t1@dblink1 a12 CROSS JOIN t1@dblink1 a13 CROSS JOIN t1@dblink1 a14 CROSS JOIN t1@dblink1 a15 CROSS JOIN t1@dblink1 a16 CROSS JOIN t1@dblink1 a17 CROSS JOIN t1@dblink1 a18 CROSS JOIN t1@dblink1 a19 CROSS JOIN t1@dblink1 a20 CROSS JOIN t1@dblink1 a21 CROSS JOIN t1@dblink1 a22 CROSS JOIN t1@dblink1 a23 CROSS JOIN t1@dblink1 a24 CROSS JOIN t1@dblink1 a25 CROSS JOIN t1@dblink1 a26 CROSS JOIN t1@dblink1 a27 CROSS JOIN t1@dblink1 a28 CROSS JOIN t1@dblink1 a29 CROSS JOIN t1@dblink1 a30 CROSS JOIN t1@dblink1 a31 CROSS JOIN t1@dblink1 a32 CROSS JOIN t1@dblink1 a33;

          C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3           C1                    C2          C3
------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- ----------- ------------ --------------------- -----------

```

如果数据库服务重启过程中进程报错内存不足，可将DBLINK_CURSOR_COUNT参数值调小或扩容内存后再尝试启动数据库服务。

### YAS-07314 too many connections for dblink %s

此错误表示当前基于同一个dblink访问远端表的次数超过系统配置值，该配置由yex_server.ini文件的`MAX_DBLINK_CONNS`参数进行约束，取值范围为[64,16384]，请参考[沙箱进程管理](yex_server沙箱进程管理)进行参数调整。可参考[dblink语法说明](dblink语法说明)了解更多的沙箱进程参数配置。


### YAS-07330 ERR\_YEX\_TOO\_MANY\_XACTS

此错误表示当前打开dblink的会话数超过了系统配置值，该配置由yex_server.ini文件的`AXS_MAX_XACTS`参数进行约束，取值范围为[1024,16384]，默认值为1024，请参考[沙箱进程管理](yex_server沙箱进程管理)进行参数调整。可参考[dblink语法说明](dblink语法说明)了解更多的沙箱进程参数配置。

### YAS-07331 transaction branches in same session can not exceed %d

一个session能同时打开的不同的dblink个数为32个，此错误表示当前session同时打开的dblink个数已超过32个。


