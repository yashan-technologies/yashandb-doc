The quick start guide introduces a minimal deployment of a standalone YashanDB database and a container version of YashanDB for trial environments, provides operation guidance for migrating from Oracle to YashanDB, and offers Java and C programming development examples, aiming to help you quickly experience the basic capabilities of YashanDB.

When you need to deploy a production database environment, please strictly refer to the [Installation and Upgrade](../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) manual for operation.

## Download Software Packages

Please visit the [Download Center](https://download.yashandb.com/download) to obtain the YashanDB database software package and related toolkits.

|Item |Description |Software Package Name |
| ---------------- | ---- | ---- |
| Database Installation Package | YashanDB {version_number}    | yashandb-{version_number}-linux-x86_64.tar.gz<br/>yashandb-{version_number}-linux-aarch64.tar.gz |
| Database Development Toolkit | YashanDB Developer Center {version_number} | yashandb-developer-center-{version_number}-amd64-installer.exe<br/>ydc-web-{version_number}-linux-x86_64.tar.gz |
| Data Migration Toolkit   | Yashan Migration Platform {version_number} | yashan-migrate-platform-{version_number}-linux-aarch64.zip<br/>yashan-migrate-platform-{version_number}-linux-x86-64.zip |
| Database Client Toolkit   | YashanDB YASQL {version_number} | yashandb-client-{version_number}-windows-amd64.zip   |
| JDBC driver Program     | YashanDB Connector/JDBC {version_number} | yashandb-jdbc-{version_number}.jar                    |
| C driver Program         | YashanDB Connector/C {version_number} | yashandb-client-{version_number}-windows-amd64.zip   |

## Prepare the Server

The table below shows the minimum configuration for a personal development trial environment. Please adjust your hardware and software configuration accordingly.

|Item |Description |
| -------- | ---------------------------- |
| Operating System | CentOS 7.6 or above or KylinOS V10, this chapter takes CentOS 7.6 as an example |
| CPU      | X86_64 4C       |
| Memory      | 8G                                                                        |
| Hard Disk   | 50G                                                                       |
| File System | ext4 or xfs                                                               |
| Network     | Gigabit Ethernet, supporting TCP and UDP connections                       |

## Deploy Trial Environment

### Solution 1: Local Deployment of Standalone Trial Environment on Server

1. Prepare the Linux server (using CentOS 7.6 system as an example), create a database installation user `yashan`, and create the installation and file directories for the database service.

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

2. Upload the database installation package to the server's `/home/yashan/install` directory via the `yashan` user and extract the database installation package.

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
   
   - The `hosts.toml` and `yashandb.toml` files are automatically generated by the `./bin/yasboot package se gen` command using the default deployment parameters for local deployment. If you need to customize deployment parameters, reference the [yasboot package](../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) to modify parameters.
   - `-p passwd` is the password parameter for the database system super administrator user `sys`, which requires a password length of 8-64 characters and must contain numbers, letters, and special characters but not the username string. This password should be managed properly after configuration.

4. Configure the environment variables.
   
   ```bash
   $ cd /home/yashan/install/{version_number}/conf
   # If YashanDB-related environment variables already exist in ~/.bashrc, remove them
   
   $ cat yashandb.bashrc >> ~/.bashrc
   $ source ~/.bashrc
   ```

5. Connect to the database using the local database client tool *yasql* as the `sys` user and check that the database status is open, indicating the database is running normally; the default connection port for the database is 1688.

   ```shell
   # su yashan
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64
   
   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> SELECT status FROM V$INSTANCE;
   
   STATUS
   -------------
   OPEN
   
   ```

   To facilitate access to the YashanDB service from remote Windows or Linux devices, you can refer to the [YashanDB Client Installation](../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) to deploy the database client tool *yasql* on the remote devices.

### Solution 2: Docker Container Deployment Trial Environment

> **Caution**: 
> 
> A single node YashanDB (yashan mode) in Standalone Deployment can be quickly and lightly deployed through Docker containers and the specific image package.

1. Prepare the server (using Linux CentOS 7.6 system as an example), create a database installation user `yashan`, and create a container mount directory.

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

   Log in to the installation server as the installation user yashan and create 2 directories under the root directory to map YashanDB's data directory and yasboot tool directory:
   
   ```bash
   $ cd ~
   $ mkdir data
   $ mkdir yashan
   ```


2. Execute the following command to import the installation package:

   ```bash
   $ docker load -i yashandb-image-{version_number}-linux-x86_64.tar.gz
   ```

   After the import is complete, you can use the `docker images` command to view the imported images, showing the following output:

   ```bash
   $ docker images
   REPOSITORY                                 TAG                                               IMAGE ID       CREATED        SIZE
   yashandb                                   yashandb-image-{version_number}-linux-x86_64   2d27c4f55115   29 hours ago   912MB
   ```

3. Execute the following command to start the container:

   ```shell
   $ docker run -d -p 1688:1688 -v /home/yashan/data:/data/yashan -v /home/yashan/yasboot:/home/yashan/.yasboot -e SYS_PASSWD=password --name yashandb yashandb:yashandb-image-{version_number}-linux-x86_64
   ```

   Parameter description:
   
   - `-p 1688:1688`: Configures port mapping in the format of host port: container port, allowing external applications to access the database via the specified host port after configuration.
   - `-v`: Specify container file path mapping, mounting YashanDB's data directory and *yasboot* tool directory prepared on the host machine into the container. For multiple directories, specify multiple times.
   - `--name yashandb`: Configures the container name.
   - `-e SYS_PASSWD=password`: Configures the password for the database super administrator `sys` user.

   Execute the following command to check the installation result:

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
   install version: yashandb {version_number}
   update host to yasom...
   +------------------------------------------------------------------------------------------------------------+
   | type | uuid             | name               | hostid | index    | status  | return_code | progress | cost |
   +------------------------------------------------------------------------------------------------------------+
   | task | cebb2746ef35401b | DeployYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 33   |
   +------+------------------+--------------------+--------+----------+---------+-------------+----------+------+
   task completed, status: SUCCESS
   ```

   When the `DeployYasdbCluster` task status is `SUCCESS`, it indicates that the database installation was successful.

4. Enter the container and connect to the database using the local database client tool *yasql* as the `sys` user, and check that the database status is open, indicating that the database is running normally. The default connection port for the database is 1688.

   ```shell
   $ docker exec -it yashandb /bin/bash -l
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64

   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> SELECT status FROM V$INSTANCE;
   
   STATUS
   -------------
   OPEN
   
   ```

To facilitate access to the database service from remote Windows or Linux devices, you can refer to the [YashanDB Client Installation](../Installation and Upgrade/Installation and Deployment/YashanDB Client Installation/00YashanDB Client Installation) to deploy the database client tool *yasql* on remote devices. When connecting to the database container, use the `sys` user password and the host-mapped port specified in the above command.

## Create Database Test User

After the default installation, YashanDB comes with a preconfigured super administrator user `sys`. You need to connect to the database with the `sys` user to create a database test user for this trial (using sales/sales as an example) and grant permissions.

   ```shell
   # su yashan
   $ yasql sys/********@192.168.1.2:1688
   YashanDB SQL Enterprise Edition Release {version_number} x86_64

   Connected to:
   YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux
   
   SQL> CREATE USER sales IDENTIFIED BY sales;
   SQL> GRANT CONNECT TO sales;
   SQL> GRANT RESOURCE TO sales;
   SQL> GRANT SELECT_CATALOG_ROLE TO sales;
   SQL> GRANT DBA TO sales;
   ```

For detailed information on the user management system, you can refer to [User Management](../产品安全/身份标识与鉴别/管理用户).