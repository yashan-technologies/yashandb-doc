Before executing the installation deployment, log in to the server 192.168.1.2 as the installation user (yashan) and enter the installation directory /home/yashan/install.

```shell
$ cd /home/yashan/install
```



## Step 1: Generate Deployment Files

### Common Parameter Description

The parameters required to generate the configuration file vary slightly depending on the size of the database to be deployed (e.g., single database, 1 master 2 standby, etc.). The commonly used parameters are shown in the table below. For the complete list of parameters, please refer to the [yasboot package de gen command](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package.html#de).

|Parameter |Description |Example Value |
|--------------------|--------------------------------------|---------|
| **Basic Parameters**|
| --cluster | Specify the database cluster name, which will also serve as the initial database name | yashandb  |
| -u,--username<br/>-p,--password       | Specify the [YashanDB installation user](../Pre-Installation Preparation/Creating an Installation User) username and password. If passwordless login is configured, the password parameter can be omitted | yashan<br/>password    | 
|--ip | Specify the IP addresses of the database server, multiple IPs separated by commas.  | [Set of Server IP Addresses](../Pre-Installation Preparation/Preparing the Servers), for example 192.168.1.2,192.168.1.3,192.168.1.4 |
|--port| Specify the SSH service port of the database server | 22   |
| -su,--sudo-username<br/>-sp,--sudo-password | Specify the SSH username and password with sudo privileges. If the installation user already has sudo privileges, this parameter can be omitted. <br/> During the installation process, some operations may use or verify sudo privileges.  For example: If resources management functionality is needed, specify --create-cgroup parameter to create cgroup directory requires sudo privilege | - |
| --install-path | Specify the database installation path (i.e., pre-planned [HOME directory](../Pre-Installation Preparation/Partitioning Directories)). Supports numbers, letters (case sensitive), and some symbols (`/`, `-`, `_`, `.`) | /data/yashan/yasdb_home  |
| --data-path | Specify the directory for data storage (i.e., pre-planned [DATA directory](../Pre-Installation Preparation/Partitioning Directories)). Supports numbers, letters (case sensitive), and some symbols (`/`, `-`, `_`, `.`) | /data/yashan/yasdb_data  |
| --log-path | Specify the [running log directory](../Pre-Installation Preparation/Partitioning Directories) for the database. Supports numbers, letters (case sensitive), and some symbols (`/`, `-`, `_`, `.`) | /data/yashan/log   |
| --listen-cidr       | Database listening address network segment; this configuration will be used for the LISTEN_ADDR parameter. The format is `192.168.1.0/24`. <br/> If a different network segment is planned for the database in production, this parameter needs to specify the planned public network segment. <br/> If no different network segment is planned, this parameter can be omitted. | Omit   |
| --replica-cidr      | The IP subnet for replication link between primary/standby nodes in the same group. This configuration will be used for the REPLICATION_ADDR parameter. The format is `192.168.1.0/24`<br/>If different subnets are planned for the database in the production environment, this parameter should be used to specify the planned private subnet.<br/>If no different subnet is planned, this parameter can be omitted. | Omit |
| --din-cidr          | The address subnet for distributed network communication, this configuration will be used for the DIN_ADDR parameter. The format is `192.168.1.0/24`<br/>If different subnets are planned for the database in the production environment, this parameter should be used to specify the planned private subnet.<br/>If no different subnet is planned, this parameter can be omitted. | Omit |
| --begin-port | Specify the listening port for the database. Other ports will be automatically generated according to rules, which can be manually modified in the generated configuration file | 1688  |
| --plugins           | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions (yashan Mode)/LSFA_LISTAGG) function(corresponding functionality is not applicable to ISC Distributed Cluster Deployment, no need to specify installation of this plugin separately)                        | Omit (default is all) |
| **Node Scale Related Parameters** |
| --mn                | The number of nodes in the distributed MN group, default is 1 | Specify as needed |
| --cn                | The number of distributed CN nodes, default is 1  | Specify as needed |
| --dn                | The number of nodes in the distributed DN group, e.g., 1-3 means 1 group with 3 nodes (1 master 2 standby) | Specify as needed |
| **Database Performance Related Parameters** | Specify as needed, can be omitted if not needed | |
| --create-cgroup     | Specify to create cgroup directory <br/>Required when installing the database that supports CPU and resource management (non-cascading standby). For details, please refer to [Configuring Resource Management](../../../Database Administration/Resource Management/Configuring Resource Management) |
| --recommend-param | Enable and use recommended configuration parameters |
| --memory-limit  | The maximum percentage of usable memory on the server, default is 80.  Must be used with --recommend-param  |
| --cpu-limit       | The maximum percentage of usable CPU on the server, default is 100.            |
| --scene           | Scenario for parameter tuning, optional [NORMAL,TPCH], defaults to NORMAL. When performing TPC-H performance testing with distributed LSC table, consider using TPCH scene parameter for tuning, and ensure that all nodes perform parameter recommendation, otherwise it may fail due to connection parameter mismatch during reboot.       |
| --scale-factor    | Data scale for testing scenarios such as TPCH, default is 100, indicating 100G test data,      |
| --table-type      | The main business table type, default is HEAP; this parameter makes the database configuration parameters more suitable for this table type.  |

### Operation Steps

This document will present several typical configuration examples. During actual installation, you can select a relatively close size example for reference.

::: tabs

== MN Node 1 Master 2 Standby + DN Node 1 Master 1 Standby

For example, use 3 servers to deploy 1 MN group (1 master 2 standby), 2 CNs, and 1 DN group (1 master 1 standby).

1. Execute the yasboot package de gen command to generate the configuration file.

    ```shell
    $ ./bin/yasboot package de gen --cluster yashandb --recommend-param \
    -u yashan -p password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    --mn 3 --cn 2 --dn 1-2
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
    # The example content is the yashandb.toml file for ISC Distributed Cluster Deployment (MN node 1 master 2 standby + DN node 1 master 1 standby), specific information subject to actual display results.

    cluster = "yashandb"    # Changes after installation will not take effect unless reinstalled
    create_simple_schema = false      # If true, example SQL will be executed after deployment. If changed to true, sys-password parameter needs to be specified during deployment
    deploy_mode = "NORMAL"    # Mode for ISC Distributed Cluster Deployment
    recommend_param = true     # Enable recommended parameters
    scale_factor = 100         # Data scale
    scene = "NORMAL"           # Parameter tuning scene
    table_type = "LSC"         # Primary business table type
    uuid = "64f1568f4f6b033e58a015a833f0ad9e"   # Automatically generated by the system, not recommended to modify
    yas_type = "DE"            # Deployment mode, changes after installation will not take effect unless reinstalled

    [[group]]
      group_type = "mn"       # Node group type, changes after installation will not take effect unless reinstalled
      name = "mng1"           # Node group name, changes after installation will not take effect unless reinstalled
      [group.config]          # All database creation parameters can be configured; defaults will be adopted if not configured
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]          # Node configuration
        cpu_limit = 8          # The upper limit of CPU cores that the node can use
        data_path = "/data/yashan/yasdb_data"  # DATA directory, changes after installation will not take effect unless reinstalled
        hostid = "host0001"    # Identification of the server where the node is located, changes after installation will not take effect unless reinstalled
        memory_limit = "1536M"  # The upper limit of memory that the node can use, related to the memory of the server used
        role = 1                # Database primary/standby role, changes after installation will not take effect unless reinstalled
        [group.node.config]     # All database parameters can be configured; defaults will be adopted if not configured, changes after installation will not take effect unless reinstalled
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.2:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.2:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.2:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "1536M"
        role = 2   
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.3:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.3:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.3:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "1536M"
        role = 2     
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300

    [[group]]
      group_type = "cn"
      name = "cng1"
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "3500M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.3:1689"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.3:1688"
          MAX_PARALLEL_WORKERS = 128
          MAX_REACTOR_CHANNELS = 1
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.3:1690"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"

      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "3500M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1689"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1688"
          MAX_PARALLEL_WORKERS = 128
          MAX_REACTOR_CHANNELS = 1
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1690"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"

    [[group]]
      group_type = "dn"
      name = "dng1"
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "7000M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1699"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1698"
          MAX_PARALLEL_WORKERS = 512
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1700"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0001"
        memory_limit = "7000M"
        role = 2
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.2:1699"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.2:1698"
          MAX_PARALLEL_WORKERS = 512
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.2:1700"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"
    ```

== MN and DN Nodes are Both 1 Master 2 Standby

For example, use 3 servers to deploy 1 MN group (1 master 2 standby), 2 CNs, and 1 DN group (1 master 2 standby).

1. Execute the yasboot package de gen command to generate the configuration file.

    ```shell
    $ ./bin/yasboot package de gen --cluster yashandb --recommend-param \
    -u yashan -p password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
    --install-path /data/yashan/yasdb_home \
    --data-path /data/yashan/yasdb_data \
    --log-path /data/yashan/log \
    --begin-port 1688 \
    --mn 3 --cn 2 --dn 1-3
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
    # The example content is the yashandb.toml file for ISC Distributed Cluster Deployment (MN and DN nodes both 1 master 2 standby), specific information subject to actual display results.

    cluster = "yashandb"    # Changes after installation will not take effect unless reinstalled
    create_simple_schema = false      # If true, example SQL will be executed after deployment. If changed to true, sys-password parameter needs to be specified during deployment
    deploy_mode = "NORMAL"    # Mode for ISC Distributed Cluster Deployment
    recommend_param = true     # Enable recommended parameters
    scale_factor = 100         # Data scale
    scene = "NORMAL"           # Parameter tuning scene
    table_type = "LSC"         # Primary business table type
    uuid = "64f1568f4f6b033e58a015a833f0ad9e"   # Automatically generated by the system, not recommended to modify
    yas_type = "DE"            # Deployment mode, changes after installation will not take effect unless reinstalled

    [[group]]
      group_type = "mn"       # Node group type, changes after installation will not take effect unless reinstalled
      name = "mng1"           # Node group name, changes after installation will not take effect unless reinstalled
      [group.config]          # All database creation parameters can be configured; defaults will be adopted if not configured
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]          # Node configuration
        cpu_limit = 8          # The upper limit of CPU cores that the node can use
        data_path = "/data/yashan/yasdb_data"  # DATA directory, changes after installation will not take effect unless reinstalled
        hostid = "host0001"    # Identification of the server where the node is located, changes after installation will not take effect unless reinstalled
        memory_limit = "1536M"  # The upper limit of memory that the node can use, related to the memory of the server used
        role = 1                # Database primary/standby role, changes after installation will not take effect unless reinstalled
        [group.node.config]     # All database parameters can be configured; defaults will be adopted if not configured, changes after installation will not take effect unless reinstalled
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.2:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.2:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.2:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "1536M"
        role = 2   
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.3:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.3:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.3:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "1536M"
        role = 2     
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1679"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1678"
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1680"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300

    [[group]]
      group_type = "cn"
      name = "cng1"
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "2625M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.3:1689"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.3:1688"
          MAX_PARALLEL_WORKERS = 128
          MAX_REACTOR_CHANNELS = 1
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.3:1690"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"

      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "2625M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1689"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1688"
          MAX_PARALLEL_WORKERS = 128
          MAX_REACTOR_CHANNELS = 1
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1690"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"

    [[group]]
      group_type = "dn"
      name = "dng1"
      [group.config]
        CHARACTER_SET = "utf8"
        ISARCHIVELOG = true
        REDO_FILE_NUM = 4
        REDO_FILE_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0003"
        memory_limit = "5250M"
        role = 1
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.4:1699"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.4:1698"
          MAX_PARALLEL_WORKERS = 512
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.4:1700"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0001"
        memory_limit = "5250M"
        role = 2
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.2:1699"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.2:1698"
          MAX_PARALLEL_WORKERS = 512
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.2:1700"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"
      
      [[group.node]]
        cpu_limit = 8
        data_path = "/data/yashan/yasdb_data"
        hostid = "host0002"
        memory_limit = "5250M"
        role = 2
        [group.node.config]
          DATA_BUFFER_SIZE = "256M"
          DB_BLOCK_SIZE = "8K"
          DDL_LOCK_TIMEOUT = 60
          DEFAULT_TABLE_TYPE = "LSC"
          DICTIONARY_CACHE_SIZE = 20
          DIN_ADDR = "192.168.1.3:1699"
          DSTB_POOL_SIZE = 10
          LARGE_POOL_SIZE = "32M"
          LISTEN_ADDR = "192.168.1.3:1698"
          MAX_PARALLEL_WORKERS = 512
          MAX_SESSIONS = 1024
          OPEN_CURSORS = 310
          REDO_BUFFER_SIZE = "8M"
          REPLICATION_ADDR = "192.168.1.3:1700"
          RUN_LOG_FILE_PATH = "/data/yashan/log"
          RUN_LOG_LEVEL = "DEBUG"
          SHARE_POOL_SIZE = "256M"
          SLOW_LOG_FILE_PATH = "/data/yashan/log"
          SQL_POOL_SIZE = 45
          TRANSACTION_LOCK_TIMEOUT = 300
          UNDO_RETENTION = 300
          WORK_AREA_HEAP_SIZE = "2M"
          WORK_AREA_POOL_SIZE = "128M"
    ```

:::

## Step 2: Execute Installation



Execute the following command to perform the software package installation.

```shell
$ ./bin/yasboot package install -t hosts.toml
```

|Parameter |Description |
|--------------------|--------------------------------------|
| -t, --toml | Path to the hosts.toml file for deploying the database cluster |



## Step 3: Database Deployment



Execute the following command to deploy the database.

```shell
$ ./bin/yasboot cluster deploy -t yashandb.toml -p password
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



## Step 4: Configure Environment Variables



After successful deployment, the directory specified by --install-path (for example, /data/yashan/yasdb_home) will generate a subdirectory `{version}/conf`, and this directory will automatically generate a YashanDB-related environment variable file named `{cluster_name}.bashrc`, which needs to be applied to the operating system.

Execute the following commands to activate the environment variables.

```shell
# Enter the directory where the environment variable file is located, e.g. /data/yashan/yasdb_home/{version}/conf
$ cd /data/yashan/yasdb_home/{version}/conf

# Activate environment variables
$ cat yashandb.bashrc >> ~/.bashrc
$ source ~/.bashrc

# Verify if Environment Variables Are Effective (Please use actual paths from the echo output)
$ echo $YASDB_DATA
/data/yashan/yasdb_data/cn-2-1
```

For detailed information about environment variables, please refer to [Initial Environment After Installation > Environment Variables](../安装后初始环境/环境变量).



## Step 5: Check Installation Results



If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    
    An example of the echoed information is as follows:

    ::: tabs

    == MN Node 1 Master 2 Standby + DN Node 1 Master 1 Standby

```shell
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address   | data_path                        |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | mn        | 1-1:1  | 56961 | open            | normal          | primary       | 192.168.1.2:1678 | /data/yashan/yasdb_data/mn-1-1   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | dn        | 3-2:7  | 57029 | open            | normal          | standby       | 192.168.1.2:1698 | /data/yashan/yasdb_data/dn-3-2   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0002 | mn        | 1-2:2  | 57024 | open            | normal          | standby       | 192.168.1.3:1678 | /data/yashan/yasdb_data/mn-1-2   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | cn        | 2-1:4  | 57024 | open            | normal          | primary       | 192.168.1.3:1688 | /data/yashan/yasdb_data/cn-2-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
| host0003 | mn        | 1-3:3  | 57021 | open            | normal          | standby       | 192.168.1.4:1678 | /data/yashan/yasdb_data/mn-1-3   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | cn        | 2-2:5  | 57021 | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/cn-2-2   |
|          +-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
|          | dn        | 3-1:6  | 57021 | open            | normal          | primary       | 192.168.1.4:1698 | /data/yashan/yasdb_data/dn-3-1   |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+------------------+----------------------------------+
```

    == MN and DN Nodes are Both 1 Master 2 Standby

```shell
+-------------------------------------------------------------------------------------------------------------------------------------------------+  
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | data_path                       |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | mn        | 1-1:1  | 56961 | open            | normal          | primary       | 192.168.1.2:1678  | /data/yashan/yasdb_data/mn-1-1  |
|          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
|          | dn        | 3-2:7  | 57029 | open            | normal          | standby       | 192.168.1.2:1698  | /data/yashan/yasdb_data/dn-3-2  |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
| host0002 | mn        | 1-2:2  | 57024 | open            | normal          | standby       | 192.168.1.3:1678  | /data/yashan/yasdb_data/mn-1-2  |
|          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
|          | cn        | 2-1:4  | 57029 | open            | normal          | primary       | 192.168.1.3:1688  | /data/yashan/yasdb_data/cn-2-1  |
|          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
|          | dn        | 3-3:8  | 57016 | open            | normal          | standby       | 192.168.1.3:1698  | /data/yashan/yasdb_data/dn-3-3  |
+----------+-------+-----------------+-----------------+---------------+--------------------+-------------------+---------------------------------+
| host0003 | mn        | 1-3:3  | 57021 | open            | normal          | standby       | 192.168.1.4:1678  | /data/yashan/yasdb_data/mn-1-3  |
|          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
|          | cn        | 2-2:5  | 57037 | open            | normal          | primary       | 192.168.1.4:1688  | /data/yashan/yasdb_data/cn-2-2  |
|          +-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
|          | dn        | 3-1:6  | 57056 | open            | normal          | primary       | 192.168.1.4:1698  | /data/yashan/yasdb_data/dn-3-1  |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+---------------------------------+
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
  ```

3. (Optional) Create a database user and grant permissions. For more operations, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/User/00User).

  ```sql
  CREATE USER sales IDENTIFIED BY sales;

  GRANT CONNECT TO SALES;

  exit
  ```



## Step 6: Start Daemon Processes



Execute the following command to start the daemon processes.

```shell
$ yasboot monit start --cluster yashandb
```

> **Note**:
>
> In subsequent usage, after the server reboots, the daemon processes will need to be manually started. The daemon will automatically bring up related database processes to start the database. To simplify operations, you may configure the daemon to start automatically on boot, indirectly achieving automatic database startup; for specific operations, please refer to [Configuring Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart).



## Step 7 (Optional): Enable Election

When the DN group has 1 master 1 standby deployment, if automatic switching between primary/standby nodes in the DN group is required, it is necessary to enable the [election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby) based on yasom.
