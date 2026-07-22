The Quick Start Guide will introduce the minimal deployment of the standalone YashanDB database in mysql mode, including basic syntax operations under mysql mode. It will provide guidance on migrating MySQL to YashanDB and offer Java language development examples, aiming to help you quickly experience the basic capabilities of YashanDB in mysql mode.

When you need to deploy a production environment database, please strictly refer to the [Installation and Upgrade](../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) manual for operations.

## Download Software Package

Please visit the [Download Center](https://download.yashandb.com/download) to obtain the YashanDB database software package and related tools.

|Item |Description |Software Package Name |
| ---------------- | ---- | ---- |
| Database Installation Package | YashanDB {version_number}  | yashandb-{version_number}-linux-x86_64.tar.gz<br/>yashandb-{version_number}-linux-aarch64.tar.gz   |
| Database Development Toolkit  | YashanDB Developer Center {version_number}  | yashandb-developer-center-{version_number}-amd64-installer.exe<br/>ydc-web-{version_number}-linux-x86_64.tar.gz  |
| Data Migration Toolkit | Yashan Migration Platform {version_number}  | yashan-migrate-platform-{version_number}-linux-aarch64.zip<br/>yashan-migrate-platform-{version_number}-linux-x86-64.zip  |
| Database Client Toolkit   | YashanDB *yasql* {version_number}  | yashandb-client-{version_number}-windows-amd64.zip                |
| JDBC driver Program      | YashanDB Connector/JDBC {version_number}  | yashandb-jdbc-{version_number}.jar                                 |
| C driver Program          | YashanDB Connector/C {version_number}  | yashandb-client-{version_number}-windows-amd64.zip                |

## Prepare the Server

The following table lists the minimum configuration for a personal development trial environment. Please adjust your software and hardware configuration according to the following specifications.

|Item |Description |
| -------- | ---------------------------- |
| Operating System | CentOS 7.6 or above or KylinOS V10, with CentOS 7.6 as an example |
| CPU      | X86_64 4C       |
| Memory       | 8G                                                       |
| Hard Disk    | 50G                                                      |
| File System  | ext4 or xfs                                             |
| Network      | Gigabit Ethernet, supporting TCP and UDP connections     |

## Deploy Trial Environment

1. Prepare a Linux server (taking CentOS 7.6 as an example), create a database installation user named yashan, and create the installation directory and file directory for the database service.

   ```bash
   $ su root
   # useradd -d /home/yashan -m yashan
   # passwd yashan
   
   Changing password for user yashan .
   New password:
   Retype new password:
   passwd: all authentication tokens updated successfully.   
   
   # groupadd YASDBA
   # usermod -aG YASDBA yashan
   # cd /
   # mkdir data
   # cd data
   # mkdir yashan
   # chown -R yashan:yashan /data/yashan
   ```

2. Use the yashan user to upload the database installation package to the server's /home/yashan/install directory and extract the database installation package.

   ```bash
   # su - yashan 
   $ mkdir install
   $ cd /home/yashan/install
   $ tar zxf yashandb-{version_number}-linux-x86_64.tar.gz
   ```

3. Use the command line tool *yasboot* to install the standalone database.

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
   
   - `--mode mysql` indicates that the YashanDB database service will be deployed in MySQL syntax mode.
   - The hosts.toml and yashandb.toml files mentioned here are automatically generated by the command ./bin/yasboot package se gen according to the default deployment parameters for local servers; during the deployment of MySQL syntax mode, the parameter `mysql_addr = "ip_addr:1690"` in the yashandb.toml file specifies the listening address for the MySQL protocol, where 1690 is the default port. You can connect to YashanDB through this listening address using MySQL clients or drivers. If you need to customize deployment parameters, you can refer to [yasboot package](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) to modify the parameters.
   - `-p passwd` is the password parameter for the database system super administrator user sys, requiring the password length to be between 8-64 characters, including numbers, letters, and special characters, but not containing the username string. The password must be managed properly after configuration.

4. Configure environment variables.
   
   ```bash
   $ cd /home/yashan/install/{version_number}/conf
   # If YashanDB related environment variables already exist in ~/.bashrc, clear them.
   
   $ cat yashandb.bashrc >> ~/.bashrc
   $ source ~/.bashrc
   ```

5. Connect to the database using the local database client tool *yasql* as the sys user. Check that the database running status is open, indicating that the database is running normally. The default connection port for the database is 1688.

   ```shell
   # su yashan
   $ yasql sys/passwd@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> select status from v$instance;
   
   status
   -------------
   OPEN
   
   SQL> SELECT database_name FROM v$database;

   database_name                                                    
   ---------------------------------------------------------------- 
   yashandb   

   # In YashanDB installed in mysql mode, all sessions created in OPEN state are in mysql mode.
   SQL> SHOW PARAMETER COMPAT_VECTOR

   name                                                             value
   ---------------------------------------------------------------- ----------------------------------------------------------------
   COMPAT_VECTOR                                                    mysql

   1 row fetched.
   ```

   For your convenience to access YashanDB from remote Windows or Linux devices, please refer to [YashanDB Client Installation](../../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) to deploy the database client tool *yasql* on remote devices.

6. Create test users for this product experience.

   ```shell
   -- Users created in mysql mode have the privilege to connect to the database by default.
   SQL> CREATE USER "sales" IDENTIFIED BY 'your_Password1';

   Succeed.

   SQL> GRANT ALL ON *.* to sales;

   Succeed.

   SQL> GRANT DBA to sales;

   Succeed.
   ```
   You can refer to [User Management](../Product Security/Managing Users) for detailed user management system information.