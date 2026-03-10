快速入门手册将介绍最小化部署单机YashanDB数据库mysql模式版本，介绍对mysql模式下的基本语法操作，对MySQL迁移YashanDB进行操作指引，并提供了Java语言开发示例，旨在帮助您快速体验YashanDB mysql模式的基本能力。

当您需要部署生产环境数据库时，请严格参考[安装和升级](../../安装和升级/安装部署/00安装部署)手册进行操作。

## 下载软件包

请前往[下载中心](https://download.yashandb.com/download)获取YashanDB数据库软件包及相关工具包。

|  项目| 描述| 软件包名称|
| ---------------- | ---- | ---- |
| 数据库安装包     | YashanDB {版本号}     | yashandb-{版本号}-linux-x86_64.tar.gz<br/>yashandb-{版本号}-linux-aarch64.tar.gz |
| 数据库开发工具包 | YashanDB Developer Center {版本号}     | yashandb-developer-center-{版本号}-amd64-installer.exe<br/>ydc-web-{版本号}-linux-x86_64.tar.gz |
| 数据迁移工具包   | Yashan Migration Platform {版本号}     | yashan-migrate-platform-{版本号}-linux-aarch64.zip<br/>yashan-migrate-platform-{版本号}-linux-x86-64.zip |
| 数据库客户端工具包 | YashanDB yasql {版本号} | yashandb-client-{版本号}-windows-amd64.zip |
| JDBC驱动程序   | YashanDB Connector/JDBC {版本号}     | yashandb-jdbc-{版本号}.jar |
| C驱动程序   | YashanDB Connector/C {版本号}     | yashandb-client-{版本号}-windows-amd64.zip |

## 准备服务器

下表为个人开发试用环境最小配置，请根据如下配置自行调整软硬件配置。

|  项目| 描述|
| -------- | ---------------------------- |
| 操作系统 | CentOS 7.6及以上或KylinOS V10，本章节以CentOS 7.6为例 |
| CPU      | X86_64 4C       |
| 内存     | 8G                           |
| 硬盘     | 50G                          |
| 文件系统 | ext4或xfs                          |
| 网络     | 千兆以太网，支持TCP和UDP链接 |

## 部署试用环境

1. 准备linux服务器（以CentOS 7.6系统为例），创建数据库安装用户yashan，并创建数据库服务的安装目录及文件目录。

   ```bash
   $ su root
   # useradd -d /home/yashan -m yashan
   # passwd yashan
   
   Changing password for user yashan .
   New password:
   Retype new password:
   passwd: all authentication tokens updated successfully.   
   
   # groupadd YASDBA
   # usermod -a -G YASDBA yashan
   # cd /
   # mkdir data
   # cd data
   # mkdir yashan
   # chown -R yashan:yashan /data/yashan
   ```

2. 通过yashan用户将数据库安装包上传到服务器/home/yashan/install目录，并解压数据库安装包。

   ```bash
   # su - yashan 
   $ mkdir install
   $ cd /home/yashan/install
   $ tar zxf yashandb-{version_number}-linux-x86_64.tar.gz
   ```

3. 通过命令行工具yasboot安装单机数据库。

   ```bash
   # su yashan
   $ cd /home/yashan/install
   $ ./bin/yasboot package se gen --cluster yashandb --local --ip 192.168.1.2 \
   --data-path /data/yashan/yasdb_data --listen-cidr 0.0.0.0/0 --mode mysql
    hostid   | group | node_type | node_name | listen_addr    | replication_addr | data_path
   --------------------------------------------------------------------------------------------------------
   host0001 | dbg1  | db        | 1-1       | 0.0.0.0:1688 | 192.168.1.2:1689   | /data/yashan/yasdb_data
   ----------+-------+-----------+-----------+----------------+------------------+-------------------------
   
   Generate config completed

   $ ls -l
   -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
   -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
   
   $ ./bin/yasboot package install -t hosts.toml
   checking install package...
   install version: yashandb {version_number}
   host0001 100% [====================================================================]   27s
   update host to yasom...

   $ ./bin/yasboot cluster deploy -t yashandb.toml -p sys_password
    type | uuid             | name               | hostid | index    | status  | return_code | progress | cost
   ------------------------------------------------------------------------------------------------------------
   task | 356b6a4a51ad600a | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 9
   ------+------------------+--------------------+--------+----------+---------+-------------+----------+------
   task completed, status: SUCCESS
   
   ```
   
   - `--mode mysql`表示YashanDB数据库服务将部署为mysql语法模式。
   - 此处的hosts.toml、yashandb.toml文件为./bin/yasboot package se gen命令按照服务器本地化部署默认部署参数自动生成；在部署mysql语法模式时，yashandb.toml文件中参数`mysql_addr = "ip_addr:1690"`为MySQL协议的监听地址，1690为默认端口，可使用MySQL客户端或驱动通过该监听地址连接到YashanDB。如需自定义部署参数，可参考[yasboot package](../../工具手册/yasboot/yasboot命令介绍/yasboot package)修改参数。
   - `-p passwd`为数据库系统超级管理员用户sys的密码参数，要求密码长度为8-64位、必须同时包含数字、字母和特殊字符但不包含用户名字符串。配置完成后需妥善管理该密码。

4. 配置环境变量。
   
   ```bash
   $ cd /home/yashan/install/{version_number}/conf
   # 如~/.bashrc中已存在YashanDB相关的环境变量，将其清除
   
   $ cat yashandb.bashrc >> ~/.bashrc
   $ source ~/.bashrc
   ```

5. 以sys用户通过服务器本地的数据库客户端工具yasql连接数据库，查看数据库运行状态为open表示数据库正常运行，数据库默认连接端口为1688。

   ```shell
   # su yashan
   $ yasql sys/passwd@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> SELECT status FROM V$INSTANCE;
   
   status
   -------------
   OPEN
   
   SQL> SELECT database_name FROM v$database;

   database_name                                                    
   ---------------------------------------------------------------- 
   yashandb   

   # 以mysql模式安装的YashanDB，在OPEN状态下新建的所有会话均为mysql模式
   SQL> SHOW PARAMETER COMPAT_VECTOR

   name                                                             value
   ---------------------------------------------------------------- ----------------------------------------------------------------
   COMPAT_VECTOR                                                    mysql

   ```

   为了方便您通过远端的Windows或Linux设备访问YashanDB，可参考[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)将数据库客户端工具yasql部署在远端设备上。

6. 为本次产品体验创建测试用户。

   ```shell
   -- mysql模式下创建的用户默认具备连接数据库的权限
   SQL> CREATE USER "sales" IDENTIFIED BY 'your_Password1';


   SQL> GRANT ALL ON *.* to sales;


   SQL> GRANT DBA to sales;

   ```
   可参考[用户管理](../产品安全/管理用户)了解详细的用户管理体系。
