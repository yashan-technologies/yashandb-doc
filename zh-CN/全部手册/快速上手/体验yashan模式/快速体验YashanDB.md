快速入门手册将介绍最小化部署单机YashanDB数据库及容器版YashanDB数据库试用环境，对Oracle迁移YashanDB进行操作指引，并提供了Java及C语言工程开发示例，旨在帮助您快速体验YashanDB基本能力。

当您需要部署生产环境数据库时，请严格参考[安装和升级](../../安装和升级/安装部署/00安装部署)手册进行操作。

## 下载软件包

请前往[下载中心](https://download.yashandb.com/download)获取YashanDB数据库软件包及相关工具包。

|  项目| 描述| 软件包名称|
| ---------------- | ---- | ---- |
| 数据库安装包     | YashanDB {版本号}     | yashandb-{版本号}-linux-x86_64.tar.gz<br/>yashandb-{版本号}-linux-aarch64.tar.gz |
| 数据库开发工具包 | YashanDB Developer Center {版本号}     | yashandb-developer-center-{版本号}-amd64-installer.exe<br/>ydc-web-{版本号}-linux-x86_64.tar.gz |
| 数据迁移工具包   | Yashan Migration Platform {版本号}     | yashan-migrate-platform-{版本号}-linux-aarch64.zip<br/>yashan-migrate-platform-{版本号}-linux-x86-64.zip |
| 数据库客户端工具包 | YashanDB YASQL {版本号} | yashandb-client-{版本号}-windows-amd64.zip |
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

### 方案一：服务器本地部署单机试用环境

1. 准备linux服务器（以CentOS 7.6系统为例），创建数据库安装用户yashan，并创建数据库服务的安装目录及文件目录。

   ```bash
   $ su root
   # useradd -d /home/yashan -m yashan
   # passwd yashan
   
   Changing password for user yashan.
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
   $ tar zxf yashandb-{版本号}-linux-x86_64.tar.gz
   ```

3. 通过命令行工具yasboot安装单机数据库。

   ```bash
   # su yashan
   $ cd /home/yashan/install
   $ ./bin/yasboot package se gen --cluster yashandb --local --listen-cidr 0.0.0.0/0 \
   --data-path /data/yashan/yasdb_data
    hostid   | group | node_type | node_name | listen_addr    | replication_addr | data_path
   --------------------------------------------------------------------------------------------------------
   host0001 | dbg1  | db        | 1-1       | 0.0.0.0:1688 | 0.0.0.0:1689   | /data/yashan/yasdb_data
   ----------+-------+-----------+-----------+----------------+------------------+-------------------------
   
   Generate config completed

   $ ls -l
   -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
   -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
   
   $ ./bin/yasboot package install -t hosts.toml
   checking install package...
   install version: yashandb {版本号}
   host0001 100% [====================================================================]   27s
   update host to yasom...

   $ ./bin/yasboot cluster deploy -t yashandb.toml -p sys_password
    type | uuid             | name               | hostid | index    | status  | return_code | progress | cost
   ------------------------------------------------------------------------------------------------------------
   task | 356b6a4a51ad600a | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 9
   ------+------------------+--------------------+--------+----------+---------+-------------+----------+------
   task completed, status: SUCCESS
   
   ```

   
   - 此处的hosts.toml、yashandb.toml文件为./bin/yasboot package se gen命令按照服务器本地化部署默认部署参数自动生成，如需自定义部署参数，可参考[yasboot package](../../工具手册/yasboot/yasboot命令介绍/yasboot package)修改参数。
   - `-p passwd`为数据库系统超级管理员用户sys的密码参数，要求密码长度为8-64位、必须同时包含数字、字母和特殊字符但不包含用户名字符串。配置完成后需妥善管理该密码。

4. 配置环境变量。
   
   ```bash
   $ cd /data/yashan/yasdb_home/{版本号}/conf
   # 如~/.bashrc中已存在YashanDB相关的环境变量，将其清除
   
   $ cat yashandb.bashrc >> ~/.bashrc
   $ source ~/.bashrc
   ```

5. 以sys用户通过服务器本地的数据库客户端工具yasql连接数据库，查看数据库运行状态为open表示数据库正常运行，数据库默认连接端口为1688。

   ```shell
   # su yashan
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {版本号} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
   
   SQL> SELECT status FROM V$INSTANCE;
   
   STATUS
   -------------
   OPEN
   
   ```

   为了方便您通过远端的Windows或Linux设备访问崖山数据库服务，可参考[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)将数据库客户端工具yasql部署在远端设备上。

### 方案二：Docker容器部署试用环境

> **Caution**: 
> 
> 通过Docker容器与特定镜像包可快速轻量化部署yashan模式的单机单节点YashanDB。

1. 准备服务器（以Linux CentOS 7.6系统为例），创建数据库安装用户yashan，并创建容器挂载目录。

   ```bash
   $ su root
   # useradd -d /home/yashan -m yashan
   # passwd yashan
   
   Changing password for user yashan.
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

2. 执行如下命令，导入安装包：

   ```bash
   $ docker load -i yashandb-image-{version_number}-linux-x86_64.tar.gz
   ```

   导入完成后，可以使用docker images命令查看导入的镜像，回显信息如下：

   ```bash
   $ docker images
   REPOSITORY                                 TAG                                               IMAGE ID       CREATED        SIZE
   yashandb                                   yashandb-image-{version_number}-linux-x86_64   2d27c4f55115   29 hours ago   912MB
   ```

3. 执行如下命令，启动容器：

   ```shell
   $ docker run -d -p 1688:1688 -v /home/yashan/yashandb:/data/yashan -e SYS_PASSWD=password --name yashandb yashandb:yashandb-image-{version number}-linux-x86_64
   ```

   参数说明：
   
   - `-p 1688:1688`：配置端口映射，格式为 主机（宿主）端口：容器端口，配置完成后外部应用可通过指定主机端口访问数据库。
   - ` -v /home/yashan/yashandb:/data/yashan`：挂载卷，格式为 主机（宿主）路径：容器路径，完成挂载可将容器数据进行持久化。
   - `--name yashandb`：配置容器名称。
   - `-e SYS_PASSWD=password`：配置数据库超级管理员sys用户的密码。

   执行如下命令，检查安装结果：

   ```shell
   $ docker logs yashandb
   init
   host host0001 openssl version: OpenSSL 1.1.1l  24 Aug 2021
   +------------------------------------------------------------------------------------------------------+
   | hostid   | group | node_type | node_name | listen_addr  | replication_addr | data_path               |
   +------------------------------------------------------------------------------------------------------+
   | host0001 | dbg1  | db        | 1-1       | 0.0.0.0:1688 | 127.0.0.1:1689   | /data/yashan/yasdb_data |
   +----------+-------+-----------+-----------+--------------+------------------+-------------------------+
   
   Generate config completed
   checking install profile.toml...
   install version: yashandb {版本号}
   update host to yasom...
   +------------------------------------------------------------------------------------------------------------+
   | type | uuid             | name               | hostid | index    | status  | return_code | progress | cost |
   +------------------------------------------------------------------------------------------------------------+
   | task | cebb2746ef35401b | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 33   |
   +------+------------------+--------------------+--------+----------+---------+-------------+----------+------+
   task completed, status: SUCCESS
   ```

   DeployYasdbCluster任务状态为SUCCESS时，表示数据库安装成功。

4. 进入容器，以sys用户通过服务器本地的数据库客户端工具yasql连接数据库，查看数据库运行状态为open表示数据库正常运行，数据库默认连接端口为1688。

   ```shell
   $ docker exec -it yashandb /bin/bash -l
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {版本号} x86_64

   Connected to:
   YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
   
   SQL> SELECT status FROM V$INSTANCE;
   
   STATUS
   -------------
   OPEN
   
   ```

为了方便您通过远端的Windows或Linux设备访问数据库服务，可参考[YashanDB客户端安装](../../安装和升级/安装部署/YashanDB客户端安装/00YashanDB客户端安装)将数据库客户端工具yasql部署在远端设备上。连接数据库容器时，使用上述命令指定的sys用户密码及主机映射端口进行连接。

## 创建数据库测试用户

默认安装完成后，YashanDB已预置超级管理员用户sys，您需通过sys用户连接数据库，为本次试用创建数据库测试用户（本文以sales/sales为例）并赋权。

   ```shell
   # su yashan
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {版本号} x86_64

   Connected to:
   YashanDB Server Enterprise Edition Release {版本号} x86_64 - Linux
   
   SQL> CREATE USER sales IDENTIFIED BY sales;
   SQL> GRANT CONNECT TO sales;
   SQL> GRANT RESOURCE TO sales;
   SQL> GRANT SELECT_CATALOG_ROLE TO sales;
   SQL> GRANT DBA TO sales;
   ```

可参考[用户管理](../../产品安全/身份标识与鉴别/用户/00用户)了解详细的用户管理体系。
