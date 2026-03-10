Before executing the installation deployment, log in to the server 192.168.1.2 as the installation user (yashan) and enter the installation directory /home/yashan/install.

```shell
$ cd /home/yashan/install
```


<span id="Interactive" name="Interactive" class="yaslink"></span>

## Interactive Deployment

The interactive deployment supports deploying a single database or primary/standby environment in yashan mode. To set up a mysql mode database, a cascading standby deployment in yashan mode, or a dual repli-group primary-standby deployment, it can only be done through [Manual Deployment](#Manual).



>**Note**:
>
> During the interactive deployment process, after entering the corresponding configuration, you must press **Enter** to submit the information (this will not be repeated in the steps). For configurations with default values (shown as `(default: xxx)`) that do not require custom adjustments, you can directly press **Enter** to proceed to the next step.
>
> To exit the program during the process, press **Ctrl** + **C**.





### Step 1: Configure Deployment Information

1. Execute the following command to enter the interactive deployment program.

    ```shell
    $ ./bin/yasboot init 
     __   __           _                    ____   ____  
     \ \ / /__ _  ___ | |__    __ _  _ __  |  _ \ | __ ) 
      \ V // _` |/ __|| '_ \  / _` || '_ \ | | | ||  _ \ 
       | || (_| |\__ \| | | || (_| || | | || |_| || |_) |
       |_| \__,_||___/|_| |_| \__,_||_| |_||____/ |____/ 

    Welcome to use YashanDB, this script will guide you to deploy YashanDB.
    ```

2. When the following information appears, enter the corresponding number as needed to select the deployment mode.

    - 1: Indicates deploying a single database environment on one server (single-host), with the default deployment on the current server, no need to enter the installation username and password again.

    - 2: Indicates deploying a primary/standby environment on multiple servers (multi-host), requiring the current server to be one of the multiple servers, and all servers' installation users (yashan) must have SSH privileges with consistent SSH usernames, passwords, and ports.

    ```shell
    which installation mode for this deployment:
    1) single-host: install locally, should not use SSH.
    2) multi-host: install YashanDB to multiple hosts, SSH user & password required.
    (default: 1)
    please select > 2
    ```

3. When the following information appears, enter the corresponding data as needed to select the deployment form.

    - 1: Indicates Standalone Deployment (SE).

    - 2: Indicates YAC Deployment (CE).

    

    ```shell
    which type of YashanDB do you want to deploy:
    1) SE: Standalone Deployment.
    2) CE: YAC Deployment.
    (default: 1)
    please select > 1
    ```

4. Enter the IP addresses of the servers planned for deploying the database.

    ```shell
    which ip address of the YashanDB Operation Manage, such as '192.168.1.2,192.168.1.3' or '192.168.1.[2-4]'
    please enter ip address > 192.168.1.2,192.168.1.3,192.168.1.4
    ```

5. (Optional) For multi-server deployment (multi-host), enter the installation user information as prompted:

    1. Enter the username, all servers' installation user usernames must be consistent.

        ```shell
        what is an SSH username used to install YashanDB
        (default: yashan)
        please enter ssh username > 
        ```

    2. Enter the password, all servers' installation user passwords must be consistent; if passwordless login is configured, this can be left empty.

        ```shell
        which SSH password
        please enter ssh password > ******
        ```

        
    3. Enter the SSH port number; all servers' SSH port numbers must be consistent.

        ```shell
        which SSH port
        (default: 22)
        please enter ssh port > 
        ```

6. Verify the server connection information.

   The following message indicates that the server connection is successful.

    ```shell
    checking SSH connection status...
   +----------------------------------------------+
   | ip            | message              | check |
   +----------------------------------------------+
   | 192.168.1.2   | connect successfully | OK    |
   +---------------+----------------------+-------+
   | 192.168.1.3   | connect successfully | OK    |
   +---------------+----------------------+-------+
   | 192.168.1.4   | connect successfully | OK    |
   +---------------+----------------------+-------+
    ```

   If the connection fails, you will need to re-enter the server information.



### Step 2: Verify Deployment Environment

During the environment verification process, if there are no issues, the program will automatically proceed to the next step without manual operation. If any check does not meet the required environment for the database, a prompt will return `ignore it and continue, [y/n] > `, and you will need to manually enter y/n to confirm:

- n: Indicates not to ignore the environmental situation, at this point the program will exit, and you can perform the relevant commands as prompted.
- y: Indicates to ignore, and the program will continue to the next step, but the deployment may ultimately fail or functionalities may be affected.



1. Check the OS configuration.

    ```shell
    checking system os...
    +-----------------------------------------------------------------------------------+
    | ip          | hostname    | cpu | memory  | firewalld | hugepage | YASDBA | check |
    +-----------------------------------------------------------------------------------+
    | 192.168.1.2 | host0001    | 8   | 31.26GB | inactive  | always   | joined | OK    |
    +-------------+-------------+-----+---------+-----------+----------+--------+-------+
    | 192.168.1.3 | host0002    | 8   | 31.26GB | inactive  | always   | joined | OK    |
    +-------------+-------------+-----+---------+-----------+----------+--------+-------+
    | 192.168.1.4 | host0003    | 4   | 15.51GB | inactive  | always   | joined | OK    |
    +-------------+-------------+-----+---------+-----------+----------+--------+-------+
    ```

2. Check the system kernel configuration.

    ```shell
    checking system sysctl...
    +------------------------------------------------------------------------+
    | ip          | swappiness | ip_local_port_range | max_map_count | check |
    +------------------------------------------------------------------------+
    | 192.168.1.2 | 0          | 102465535           | 2000000       | OK    |
    +-------------+------------+---------------------+---------------+-------+
    | 192.168.1.3 | 0          | 3276860999          | 2000000       | OK    |
    +-------------+------------+---------------------+---------------+-------+
    | 192.168.1.4 | 30         | 3276860999          | 65530         | OK    |
    +-------------+------------+---------------------+---------------+-------+
    ```

3. Check the limit on system resources.

    ```shell
    checking system ulimit...
    +--------------------------------------------------------------------------------------+
    | ip          | open files | max user processes | max memory size | stack size | check |
    +--------------------------------------------------------------------------------------+
    | 192.168.1.2 | 1048576    | 65535              | unlimited       | 8192       | FAIL  |
    +-------------+------------+--------------------+-----------------+------------+-------+
    | 192.168.1.3 | 1048576    | 65535              | unlimited       | 8192       | FAIL  |
    +-------------+------------+--------------------+-----------------+------------+-------+
    | 192.168.1.4 | 1048576    | 65535              | unlimited       | 8192       | FAIL  |
    +-------------+------------+--------------------+-----------------+------------+-------+

    this system deployment environment, some ulimit configure seems bad.
    please update with ulimit under root user, such as:
    echo "* soft nproc 1048576">> /etc/security/limits.conf
    echo "* hard nproc 1048576">> /etc/security/limits.conf

    if configure failed, please resolved by yourself, maybe need to reboot system.
    you can also ignore it and continue, but it is not friendly to the YashanDB and may affect performance or system stability
    ignore and continue [y/n] > 
    ```

4. Check the dependent libraries.

    ```shell
    checking system libraries...
    +-------------------------------------------------------------------------------------------------------------------+
    | ip          | os     | libcrypto           | libssl           | liblz4          | libz          | libzstd | check |
    +-------------------------------------------------------------------------------------------------------------------+
    | 192.168.1.2 | centos | libcrypto.so.1.1.1k | libssl.so.1.1.1k | liblz4.so.1.8.3 | libz.so.1.2.7 | unknow  | FAIL  |
    +-------------+--------+---------------------+------------------+-----------------+---------------+---------+-------+
    | 192.168.1.3 | centos | libcrypto.so.1.1.1k | libssl.so.1.1.1k | liblz4.so.1.8.3 | libz.so.1.2.7 | unknow  | FAIL  |
    +-------------+--------+---------------------+------------------+-----------------+---------------+---------+-------+
    | 192.168.1.4 | centos | libcrypto.so.1.1.1k | libssl.so.1.1.1k | liblz4.so.10.7.5 | libz.so.1.2.7 | unknow  | FAIL  |
    +-------------+--------+---------------------+------------------+-----------------+---------------+---------+-------+

    this system deployment environment, some libraries unfound.
    please install them by yourself, such as:
    sudo yum install zlib lz4 libzstd

    if configure failed, please resolved by yourself, also you should confiure 'LD_LIBRARY_PATH' to resolved
    you can also ignore it and continue, but deployment maybe fail
    ignore and continue [y/n] > 
    ```



### Step 3: Configure Database-Related Information

1. Enter the database name.

    ```shell
    what is the name of the YashanDB
    (default: yashandb)
    please enter cluster name(db name) > 
    ```



2. Set the primary/standby scale by entering the total number of primary and standby databases.

    Enter 1 for single database deployment; enter 2 for one-primary/one-standby; and so on.

    ```shell
    how many nodes are there in the YashanDB
    (default: 3)
    please enter the number of nodes > 
    ```

3. Select the plugins to install, using commas `,` to separate multiple values.

    |Option |Description |
    | ------------ |------------|
    | 1/none  |  Do not install plugins  |
    | 2/all  |  Install all plugins, default value  |
    | 3/gis  |  Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions/GIS Function/00GIS Function) function  |
    | 4/dblink  |  Install plugins for DBLink related functionality  |
    | 5/udf  |  Install plugins for [ UDF ](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality  |
    | 6/s3  |  Install plugins for S3 bucket related functionality  |
    | 7/listagg  |  Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions/LSFA_LISTAGG) function  |
    | 8/xml  |  Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions/XML Function/00XML Function) function  |

    
    ```shell
    what plugins do you want to intall for the YashanDB
    1) none: do not install any plugins.
    2) all: install all plugins below.
    3) gis
    4) dblink
    5) udf
    6) s3
    7) listagg
    8) xml
    you can input such as: 3,4,5,6 or gis,dblink,udf,s3
    (default: 2)
    please select > 
    ```

4. Configure the required directories for the database based on the [directory partitioning](../Pre-Installation Preparation/Partitioning Directories) plan:

    1. ) HOME directory:

    ```shell
    which install-path of the YashanDB, YASDB_HOME will be: <install-path>/<version>
    (default: /home/yashan/install)
    please enter install-path > /data/yashan/yasdb_home
    YASDB_HOME will be: /data/yashan/yasdb_home/{version_number}
    ```

    2. ) DATA directory:

    ```shell
    which data-path of the YashanDB, YASDB_DATA will be: <data-path>/db-<nodeid>
    (default: /data/yashan/yasdb_home/yasdb_data)
    please enter data-path > /data/yashan/yasdb_data
    ```

    3. ) Log directory:

    ```shell
    which log-path of the YashanDB, run.log will be: <log-path>/db-<nodeid>/run/run.log
    (default: /data/yashan/yasdb_home/log)
    please enter log-path > /data/yashan/log
    ```

5. Select the IP address for the database listener.

    ```shell
    which ip you will be used to listen
    1) manage-ip: listen on 192.168.1.2,192.168.1.3,192.168.1.4
    2) 0.0.0.0: listen on 0.0.0.0
    (default: 1)
    please select > 
    ```

6. Set the initial password for the super administrator sys user with the following requirements:

    - Password length should be 8 - 64 characters.
    
    - The password must not include the corresponding database username.
    
    - The password must contain numbers, letters, and special characters.
    
    - OS command-related special characters (such as `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped.

    ```shell
    which sys password for YashanDB
    please enter sys password > ********
    checking sys password now, please wait...
    ```

    

7. Configure the memory quota available for the database (i.e., the percentage of the total memory of the corresponding server).

   If the actual available value is lower than the configured value, you can choose whether to ignore it based on the prompt (y/n). If you choose to ignore (input y), the process will automatically proceed to the next step, but this may ultimately lead to deployment failure. If you do not ignore (input n), you will need to re-enter a proper memory percentage.

    ```shell
    which percentage of host memory available for YashanDB, 0 is unlimited
    (default: 80)
    please enter memory limit > 80
    host 192.168.1.2 memory limit is 25609M, but host available memory is 21018M
    it may cause database deployment failure
    ignore and continue [y/n] > n

    [error] not ignore warning, input again
    please retry, or use Ctrl-C to exit.
    please enter memory limit > 
    ```

10. Configure the starting port.
    
    When deploying YashanDB, all required port numbers will be generated based on this value.

    ```shell
    which port generate for YashanDB
    (default: 1688)
    please enter port > 
    ```

    



### Step 4: Deploy the Database

Choose whether to deploy YashanDB as prompted:

- 1: Config Only (config-only), indicates that only the configuration file based on the above configuration values will be generated in the current directory, and the interactive program will exit automatically. The subsequent deployment can be performed based on the configuration file without needing to specify the --sys-password parameter again.
- 2: Install Now (install-now), indicates that YashanDB will be deployed immediately based on the above configuration values.

```shell
bootloader for parameter setting has been completed, please select:
1) config-only: only generate configuration, do not install now.
                you should update those configuration, such as: database building parameters.
                then install with those configuration by yourself.
2) install-now: generate configuration and install now
(default: 2)
```

### Step 5: Post-Deployment Actions

#### Deployment Successful

The return message below indicates that the database deployment was successful.

```shell
generate configuration for host and YashanDB...
generate configuration succeed
install software...
install software succeed
initialize database...
initialize database succeed
```

After successful database deployment, you can perform the following configurations as required:

- Configure environment variables

    ```shell
    Do you want to append environment to ~/.bashrc on each machine?
    (default: yes)
    [y/n] > y

    append environment to ~/.bashrc successfully
    please source environment by yourself, such as:
    source ~/.bashrc
    ```

- Configure the monit daemon and set to start automatically on boot

    This operation is the same as that introduced in [Configuring Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart), and can be executed alternatively.

    

    ```shell
    Do you want to enable the monit function?
    If this feature is enabled, when process crashes for various reasons, it will be pulled up .
    However, if you use yasql to shutdown database, yasdb process will not be pulled up.
    [y/n] > y

    enable the daemon function successfully

    Already generate scripts: yashandb_boot_start.sh in current directory
    If you want to set monit to start automatically at boot, you can execute it in each machine, such as: 
    sudo ./yashandb_boot_start.sh
    ```

#### Deployment Failed

The return message below indicates that the database deployment has failed. You can perform environment cleanup based on the prompt, and after cleanup, the interactive deployment program will exit. To redeploy, please repeat all the above operations.

```shell
generate configuration for host and YashanDB...
generate configuration succeed
install software...
install software succeed
initialize database...
initialize database failed

cluster deploy failed, stdout:database recommend parameter is enabled: 
node 1-1, type: db, percent of memory_limit: 99%(32011M), percent of cpu_limit: 100%(8 cores)

task completed, status: FAILED
retcode: 1
stdout: apply recommend param when start node nomount
stderr: wait node 1-1 process start failed: Starting instance nomount
YAS-00101 cannot allocate 31377653760 bytes for Global Area
Failed to start instance

, stderr:
Do you want to clean this failed database?
[y/n] > y

clean success
```



### Step 6: Check Installation Result

After successful deployment, you can check the database-related information based on this operation.

1. Execute the following command to check the database status.

  ```shell
  $ yasboot cluster status -c yashandb -d
  +-----------------------------------------------------------------------------------------------------------------------------------------------+
  | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
  +-----------------------------------------------------------------------------------------------------------------------------------------------+
  | host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
  | host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
  | host0003 | db        | 1-3:3  | 49582 | open            | normal          | standby       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3 |
  +----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
  ```



2. Use the [yasql](../../../Tools Guide/yasql/User Guide for yasql) tool to connect to the database and check the instance status.

  ```shell
  $ yasql sys/********@192.168.1.2:1688
  SQL> SELECT STATUS FROM V$INSTANCE;

  STATUS        
  ------------- 
  OPEN        

  SQL> SELECT database_name FROM v$database;

  DATABASE_NAME                                                    
  ---------------------------------------------------------------- 
  yashandb     
  ```

3. (Optional) Create a database user and grant permissions. For more operations, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/Managing Users).

  ```sql
  CREATE USER sales IDENTIFIED BY sales;

  GRANT CONNECT TO SALES;

  exit
  ```



<span id="Manual" name="Manual" class="yaslink"></span>

## Manual Deployment

### Step 1: Generate Configuration File

Depending on the size of the database to be deployed (e.g., single database, 1 primary 2 standby, etc.), the parameters that need to be specified when generating the configuration file vary slightly. The common parameters are shown in the table below; for a complete list of parameters, please refer to the [yasboot package se gen command](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package).

This article will introduce several relatively typical configuration examples; during actual installation, you can choose a relatively close-scale example as a reference.

::: tabs

== Single Database



1. Execute the yasboot package se gen command to generate the configuration file. To enable MySQL compatibility mode during deployment, the `--mode mysql` parameter is required.



    ```shell
    $ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
    -u yashan -p yashan_password --ip 192.168.1.2 --port 22 \
    --install-path /data/yashan/yasdb_home  \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    # If deploying in mysql mode, please add the following command option
    --mode mysql
    ```


    
  After completion, the current directory will generate two configuration files: {cluster_name}.toml and hosts.toml:

  ```shell
  -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
  -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
  ```




2. Manually modify the relevant parameters in the configuration file according to the actual needs:

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    - The server configuration file hosts.toml: You can modify the listening ports, log storage paths, etc. for yasom and yasagent as needed.

    - The database cluster configuration file yashandb.toml: You can set all database creation parameters at the group level and all configuration parameters at the node level for YashanDB.


    
    ```toml
    # Example content for the mysql mode single database deployment yashandb.toml file; specific information is subject to the actual results

    cluster = "yashandb"    # This will not take effect if modified after installation, unless reinstalled
    create_simple_schema = false # If true, will execute the example SQL upon deployment; if changed to true, you need to specify the sys-password parameter at deploy time
    mode = "MYSQL"          # Database server syntax mode
    recommend_param = true  # Enable recommended parameters
    scale_factor = 100      # Data scale
    scene = "NORMAL"        # Parameter tuning scenario
    table_type = "HEAP"     # Main business table type; only HEAP tables are supported in mysql mode
    uuid = "64f04f0e999c72db72bd565762e6a4ea" # System generated; it is not recommended to modify
    yas_type = "SE"         # Deployment type; this will not take effect if modified after installation, unless reinstalled

    [[group]]
      group_type = "db"     # This will not take effect if modified after installation, unless reinstalled
      name = "dbg1"         # This will not take effect if modified after installation, unless reinstalled
      [group.config]        # All database creation parameters can be configured here; if not configured, default values will be used
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
        TIME_ZONE = ""

      [[group.node]]
        data_path = "/data/yashan/yasdb_data" # DATA directory; this will not take effect if modified after installation, unless reinstalled
        hostid = "host0001" # This will not take effect if modified after installation, unless reinstalled
        memory_limit = "-" # Node memory limit; relates to the memory of the server used
        mysql_addr = "192.168.1.2:1690" # The listening address for the MySQL protocol; only relevant in mysql mode
        role = 1    # Database primary/standby role; this will not take effect if modified after installation, unless reinstalled
        [group.node.config] # All database parameters can be configured here; if not configured, default values will be used and changes made post-installation can take effect (read-only parameters cannot be modified)      
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          EMPTY_STRING_AS_NULL = false  # This parameter will automatically set to FALSE when the database service is in mysql mode and cannot be changed manually
          LISTEN_ADDR = "192.168.1.2:1688"
          REPLICATION_ADDR = "192.168.1.2:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config] # For databases deployed in mysql syntax mode, you can configure all globally modifiable variables in mysql mode
    ```

== 1 Primary 2 Standby



1. Execute the yasboot package se gen command to generate the configuration file. To enable MySQL compatibility mode during deployment, the `--mode mysql` parameter is required.



    ```shell
    $ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
    -u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
    --install-path /data/yashan/yasdb_home  \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 --node 3
    # If deploying in mysql mode, please add the following command option
    --mode mysql
    ```


    
  After completion, the current directory will generate two configuration files: {cluster_name}.toml and hosts.toml:

  ```shell
  -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
  -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
  ```





2. Manually modify the relevant parameters in the configuration file according to the actual needs:

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    - The server configuration file hosts.toml: You can modify the listening ports, log storage paths, etc. for yasom and yasagent as needed.

    - The database cluster configuration file yashandb.toml: You can set all database creation parameters at the group level and all configuration parameters at the node level for YashanDB.



      When installing a high-availability environment, you also need to understand the following configuration parameters closely related to primary/standby deployment. During installation, such parameters generally have automatic configuration rules and are **not required to be manually intervened**.

      |Parameter Name |Parameter Description |Configuration Method |
      |--------------|-------------------------|--------------|
      | [REPLICATION_ADDR](../../../Reference Manual/Configuration Parameters.html#REPLICATION_ADDR) | Current database/node's IP and synchronization replication port; port number = listening port + 1. | Automatically configured |
      | [ARCHIVE_DEST_*](../../../Reference Manual/Configuration Parameters.html#ARCHIVE_DEST) | Opposite end database/node link (there are 32 such parameters, ARCHIVE_DEST_1 ~ 32, each representing the link to one standby database), need to be configured after REPLICATION_ADDR takes effect. <br/> The ARCHIVE_DEST_* parameter takes effect immediately after configuration, occupying the respective replication link and not allowing direct modification (direct modification will return error YAS-02425). If this parameter needs to be reconfigured, it must first be set to empty to release the link and then reconfigured properly. | Automatically configured |
      | [DB_FILE_NAME_CONVERT](../../../Reference Manual/Configuration Parameters.html#DB_FILE_NAME_CONVERT)<br/>[REDO_FILE_NAME_CONVERT](../../../Reference Manual/Configuration Parameters.html#REDO_FILE_NAME_CONVERT)<br/>[DB_BUCKET_NAME_CONVERT](../../../Reference Manual/Configuration Parameters.html#DB_BUCKET_NAME_CONVERT) |  Paths for converting data files, log files, and the bucket paths defined in LSC tables.<br/> When the corresponding paths of primary and standby databases do not match, all nodes need to set conversion paths, and only the parent path needs to match so that all subfolders can be converted to the target folder. | Automatically configured |
      | [HA_ELECTION_ENABLED](../../../Reference Manual/Configuration Parameters.html#HA_ELECTION_ENABLED)  | The master group in one-primary/multi-standby [leader election](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby) functionality. | This parameter is automatically enabled and enforced during installations of 3 nodes or more; if you need to disable it, it can only be done manually after installation. |
      | [HA_SSL_ENABLE](../../../Reference Manual/Configuration Parameters.html#HA_SSL_ENABLE)([SSL_ENABLE](../../../Reference Manual/Configuration Parameters.html#SSL_ENABLE))| The switch for the SSL trusted channel between primary and standby; the switch parameter must be consistent between primary and standby databases. <br/> When REPLICATION_ADDR and LISTEN_ADDR share ports, refer to SSL_ENABLE for the trusted channel between primary and standby; otherwise, refer to HA_SSL_ENABLE. <br/> When enabling the trusted channel, primary and standby need to configure SSL_CERT_FILE/SSL_KEY_FILE/SSL_DH_PARAM_FILE parameters. | It is recommended to manually configure based on needs after installation; refer to [SSL Trusted Channel Between HA Nodes](../../../Database Administration/Basic Database Management/SSL Trusted Channel Configuration/SSL Trusted Channel Between HA Nodes) for configuration methods. |

      > **Caution**: 
      >
      > If the primary and standby path conversion configuration is incorrect or not configured, the standby database will default to creating data files with the same name and path as the primary database (if the primary and standby databases are deployed on the same server, this scenario may lead to unknown errors). This might cause the standby database to misread or inadvertently modify files. If creating files of the same name fails for the standby database and default names are used during creation, then the standby database apply process will hang and report a warning. At this point, it is necessary to perform a shutdown abort to close the standby database. 
      >   - The default name format for data files is UNNAME_DATAFILE_{tablespace_id}_{datafile_id} with a default path of $YASDB_DATA/dbfiles.
      >
      >   - The default name for redo files is UNNAME_REDOFILE_{instance_id}_{redo_file_id} with a default path of $YASDB_DATA/dbfiles.
      >
      >   - The default name for bucket folders is UNNAME_BUCKET_{tablespace_id}_{bucket_id} with a default path of $YASDB_DATA/local_fs.

    ```toml
    # Example content for the yashan mode 1 primary 2 standby deployment yashandb.toml file; specific information is subject to actual results

    cluster = "yashandb"    # This will not take effect if modified after installation, unless reinstalled
    create_simple_schema = false # If true, will execute the example SQL upon deployment; if changed to true, you need to specify the sys-password parameter at deploy time
    mode = "YASHAN"         # Database server syntax mode
    recommend_param = true  # Enable recommended parameters
    scale_factor = 100      # Data scale
    scene = "NORMAL"        # Parameter tuning scenario
    table_type = "HEAP"      # Main business table type
    uuid = "64f04f0e999c72db72bd565762e6a4ea" # This is system-generated; it is not recommended to modify
    yas_type = "SE" # Deployment mode; this will not take effect if modified after installation, unless reinstalled

    [[group]]
      group_type = "db" # This will not take effect if modified after installation, unless reinstalled
      name = "dbg1" # This will not take effect if modified after installation, unless reinstalled
      [group.config] # All database creation parameters can be configured here; if not configured, default values will be used
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"

      [[group.node]]
        data_path = "/data/yashan/yasdb_data" # DATA directory; this will not take effect if modified after installation, unless reinstalled
        hostid = "host0001" # This will not take effect if modified after installation, unless reinstalled
        memory_limit = "-"
        role = 1    # Database primary/standby role; this will not take effect if modified after installation, unless reinstalled
        [group.node.config] # All database parameters can be configured here; if not configured, default values will be used and changes made post-installation can take effect (read-only parameters cannot be modified) 
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.2:1688"
          REPLICATION_ADDR = "192.168.1.2:1689"  
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config] # For databases deployed in mysql syntax mode, you can configure all globally modifiable variables in mysql mode

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "-"
        role = 2
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.3:1688"
          REPLICATION_ADDR = "192.168.1.3:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]
      
      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "-"
        role = 2
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.4:1688"
          REPLICATION_ADDR = "192.168.1.4:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH =  "/data/yashan/log"
        [group.node.mysql_config]
    ```

== 1 Primary 1 Standby 1 Cascading Standby



1. Execute the yasboot package se gen command to generate the configuration file. To enable MySQL compatibility mode during deployment, the `--mode mysql` parameter is required.



    ```shell
    $ ./bin/yasboot package se gen --cluster yashandb \
    -u yashan -p password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
    --install-path /data/yashan/yasdb_home  \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    --node 2 \
    --cascade-node 1 --cascade-parent 1
    ```


    
  After completion, the current directory will generate two configuration files: {cluster_name}.toml and hosts.toml:

  ```shell
  -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
  -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
  ```





2. Manually modify the relevant parameters in the configuration file according to the actual needs:

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    - The server configuration file hosts.toml: You can modify the listening ports, log storage paths, etc. for yasom and yasagent as needed.

    - The database cluster configuration file yashandb.toml: You can set all database creation parameters at the group level and all configuration parameters at the node level for YashanDB.



    ```toml
    # Example content for the yashan mode 1 primary 1 standby 1 cascading standby deployment in the yashandb.toml file; specific information is subject to actual results

    cluster = "yashandb"    # This will not take effect if modified after installation, unless reinstalled
    create_simple_schema = false # If true, will execute the example SQL upon deployment; if changed to true, you need to specify the sys-password parameter at deploy time
    mode = "YASHAN"         # Database server syntax mode
    uuid = "197321238defb4f8f3e6699a18504296"
    yas_type = "SE" # Deployment mode; this will not take effect if modified after installation, unless reinstalled

    [[group]]
      group_type = "db" # This will not take effect if modified after installation, unless reinstalled
      name = "dbg1" # This will not take effect if modified after installation, unless reinstalled
      [group.config] # All database creation parameters can be configured here; if not configured, default values will be used
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
        TIME_ZONE = ""

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"  # DATA directory; this will not take effect if modified after installation, unless reinstalled
        hostid = "host0001"  # This will not take effect if modified after installation, unless reinstalled
        memory_limit = "-"
        role = 1    # Database primary/standby role; this will not take effect if modified after installation, unless reinstalled
        [group.node.config] # All database parameters can be configured here; if not configured, default values will be used and changes made post-installation can take effect
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.2:1688"
          REPLICATION_ADDR = "192.168.1.2:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
        [group.node.mysql_config]

      [[group.node]]
        cascade_parent = true  #  The upper-level standby database of the cascading standby 
        data_path = "/data/yashan/yasdb_data"  # DATA directory; this will not take effect if modified after installation, unless reinstalled
        hostid = "host0002"  # This will not take effect if modified after installation, unless reinstalled
        role = 2  # Database primary/standby role; this will not take effect if modified after installation, unless reinstalled
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.3:1688"
          REPLICATION_ADDR = "192.168.1.3:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
        [group.node.mysql_config]

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"  # DATA directory; this will not take effect if modified after installation, unless reinstalled
        hostid = "host0003"  # This will not take effect if modified after installation, unless reinstalled
        role = 3      # For the cascading standby
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.4:1688"
          REPLICATION_ADDR = "192.168.1.4:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/yasdb_home/log"
        [group.node.mysql_config]
    ```

== Dual Repli-Group Primary-Standby Deployment

[Dual Replication Group Primary/Standby Deployment](../../../High Availability/Overview of YashanDB High Availability.html#dual_rep_group) is divided into primary replication group and standby replication group, with each group's servers required to be installed in different locations/data centers.



1. Execute the yasboot package se gen command to generate the configuration file. To enable MySQL compatibility mode during deployment, the `--mode mysql` parameter is required.



    ```shell
    $ ./bin/yasboot package se gen --cluster yashandb \
    -u yashan -p yashan_password  \
    --ip 192.168.1.2,192.168.1.3,192.168.1.4,192.168.10.5,192.168.10.6,192.168.10.7 --port 22  \
    --install-path /data/yashan/yasdb_home  \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    --group 2 --node 3 --standby-node 3
    ```


    
  After completion, the current directory will generate two configuration files: {cluster_name}.toml and hosts.toml:

  ```shell
  -rw-------. 1 yashan yashan       444 Aug 31 16:27 hosts.toml
  -rw-------. 1 yashan yashan       708 Aug 31 16:27 yashandb.toml
  ```





2. Manually modify the relevant parameters in the configuration file according to the actual needs:

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    - The server configuration file hosts.toml: You can modify the listening ports, log storage paths, etc. for yasom and yasagent as needed.

    - The database cluster configuration file yashandb.toml: You can set all database creation parameters at the group level and all configuration parameters at the node level for YashanDB.



    ```toml
    # Example content for the yashandb.toml file of the dual repli-group primary-standby deployment in yashan mode; specific information is subject to actual results

    cluster = "yashandb"    # This will not take effect if modified after installation, unless reinstalled
    create_simple_schema = false # If true, will execute the example SQL upon deployment; if changed to true, you need to specify the sys-password parameter at deploy time
    mode = "YASHAN"         # Database server syntax mode
    uuid = "197321238defb4f8f3e6699a18504296"
    yas_type = "SE" # Deployment mode; this will not take effect if modified after installation, unless reinstalled

    [[group]]  # Configuration for the first replication group; by default, the first replication group is the primary group
      group_type = "db"
      name = "dbg1"  
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
        TIME_ZONE = ""

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0001"
        role = 1        # Primary node in the replication group
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.2:1688"
          REPLICATION_ADDR = "192.168.1.2:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        role = 2        # Backup node in the primary group
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.3:1688"
          REPLICATION_ADDR = "192.168.1.3:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        role = 2      # Backup node in the primary group
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.1.4:1688"
          REPLICATION_ADDR = "192.168.1.4:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]

    [[group]]  # Second replication group
      group_type = "db"
      name = "dbg2"   
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
        TIME_ZONE = ""

      [[group.node]]
        cascade_parent = true
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0004"
        role = 2      # Primary asynchronous standby database node
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.10.5:1688"
          REPLICATION_ADDR = "192.168.10.5:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0005"
        role = 3      # Other nodes in the backup group, i.e., cascading standby
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.10.6:1688"
          REPLICATION_ADDR = "192.168.10.6:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]

      [[group.node]]
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0006"
        role = 3      # Other nodes in the backup group, i.e., cascading standby
        [group.node.config]
          ARCH_CLEAN_IGNORE_MODE = "BACKUP"
          CGROUP_ROOT_DIR = "/sys/fs/cgroup"
          LISTEN_ADDR = "192.168.10.7:1688"
          REPLICATION_ADDR = "192.168.10.7:1689"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "INFO"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
        [group.node.mysql_config]
    ```

:::

### Step 2: Execute Installation



Execute the following command to perform the software package installation.

```shell
$ ./bin/yasboot package install -t hosts.toml
```

|Parameter |Description |
|--------------------|--------------------------------------|
| -t, --toml | Path to the hosts.toml file for deploying the database cluster |



### Step 3: Deploy the Database



Execute the following command to deploy the database.

```shell
$ ./bin/yasboot cluster deploy -t yashandb.toml -p sys_password
```

|Parameter |Description |
|--------------------|--------------------------------------|
| -t, --toml | Path to the {cluster_name}.toml file for deploying the database cluster |
| -p, --sys-password | Set the password for the super administrator sys user; the configuration requirements are as follows:<br/>* Password length is 8 - 64 characters<br/>* The password must not contain the corresponding database username<br/>* The password must contain numbers, letters, and special characters<br/>* Special characters related to OS commands (like `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped  |

> **Note**:
>
> You can also specify the `-d, --child` parameter to display task and subtask information to understand the deployment progress.

The return message as below indicates successful deployment.

```shell
task completed, status: SUCCESS
```



### Step 4: Configure Environment Variables



After successful deployment, the directory specified by --install-path (for example, /data/yashan/yasdb_home) will generate a subdirectory `{version_number}/conf`, and this directory will automatically generate a YashanDB-related environment variable file named `{cluster_name}.bashrc`, which needs to be applied to the OS.

Execute the following commands to activate the environment variables.

```shell
# Enter the directory where the environment variable file is located, e.g. /data/yashan/yasdb_home/{version_number}/conf
$ cd /data/yashan/yasdb_home/{version_number}/conf

# Activate environment variables
$ cat yashandb.bashrc >> ~/.bashrc
$ source ~/.bashrc

# Verify if Environment Variables Are Effective (Please use actual paths from the echo output)
$ echo $YASDB_DATA
/data/yashan/yasdb_data/db-1-1
```

For detailed information about environment variables, please refer to [Initial Environment After Installation > Environment Variables](../安装后初始环境/环境变量).



### Step 5: Check Installation Result



If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```
    
    An example of the echoed information is as follows:

    ::: tabs

    == Single Database

```shell
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                        |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-----------------------------------------------------+
```

    == 1 Primary 2 Standby

```shell
+-----------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                      |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0001 | db        | 1-1:1  | 54814 | open            | normal          | primary       | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0002 | db        | 1-2:2  | 49529 | open            | normal          | standby       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
| host0003 | db        | 1-3:3  | 49582 | open            | normal          | standby       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+--------------------------------+
```

    == 1 Primary 1 Standby 1 Cascade Backup

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | source_node | listen_address  | data_path                        |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 73152 | open            | normal          | primary       | -           | 192.168.1.2:1688 | /data/yashan/yasdb_data/db-1-1  |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+------------------+---------------------------------+
| host0002 | db        | 1-2:2  | 73211 | open            | normal          | standby       | 1-1:1       | 192.168.1.3:1688 | /data/yashan/yasdb_data/db-1-2  |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+------------------+---------------------------------+
| host0003 | db        | 1-3:3  | 73403 | open            | normal          | standby       | 1-2:2       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3  |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+------------------+---------------------------------+
```

    == Dual Repli-Group Primary-Standby Deployment

```shell
+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | source_node | listen_address   | data_path                         |
+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 73132 | open            | normal          | primary       | -           | 192.168.1.2:1688  | /data/yashan/yasdb_data/db-1-1    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0002 | db        | 1-2:2  | 73199 | open            | normal          | standby       | 1-1:1       | 192.168.1.3:1688  | /data/yashan/yasdb_data/db-1-2    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0003 | db        | 1-3:3  | 73201 | open            | normal          | standby       | 1-1:1       | 192.168.1.4:1688  | /data/yashan/yasdb_data/db-1-3    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0004 | db        | 2-1:4  | 73362 | open            | normal          | standby       | 1-1:1       | 192.168.10.5:1688 | /data/yashan/yasdb_data/db-2-1    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0005 | db        | 2-2:5  | 73435 | open            | normal          | standby       | 2-1:4       | 192.168.10.6:1688 | /data/yashan/yasdb_data/db-2-2    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
| host0006 | db        | 2-3:6  | 73499 | open            | normal          | standby       | 2-1:4       | 192.168.10.7:1688 | /data/yashan/yasdb_data/db-2-3    |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------+-------------------+-----------------------------------+
```

    :::

2. Use the [yasql](../../../Tools Guide/yasql/User Guide for yasql) tool to connect to the database and check the instance status.

    ```shell
    $ yasql sys/********@192.168.1.2:1688
    SQL> SELECT STATUS FROM V$INSTANCE;

    STATUS        
    ------------- 
    OPEN        

    SQL> SELECT database_name FROM v$database;

    DATABASE_NAME                                                    
    ---------------------------------------------------------------- 
    yashandb   

    # In OPEN status, you can check the current session's syntax mode
    SQL> SHOW PARAMETER COMPAT_VECTOR

    name                                                             value
    ---------------------------------------------------------------- ----------------------------------------------------------------
    COMPAT_VECTOR                                                    yashan
    ```

3. (Optional) Create a database user and grant permissions. For more operations, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/Managing Users).

    ```shell
    SQL> CREATE USER sales IDENTIFIED BY sales;

    SQL> GRANT CONNECT TO SALES;
    
    SQL> exit
    ```

### Step 6: Start Daemon Processes



Execute the following command to start the daemon processes.

```shell
$ yasboot monit start --cluster yashandb
```

> **Note**:
>
> In subsequent usage, after the server reboots, the daemon processes will need to be manually started. The daemon will automatically bring up related database processes to start the database. To simplify operations, you may configure the daemon to start automatically on boot, indirectly achieving automatic database startup; for specific operations, please refer to [Configuring Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart).


