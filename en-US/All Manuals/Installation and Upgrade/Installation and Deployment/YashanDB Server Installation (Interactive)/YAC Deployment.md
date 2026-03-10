

## Installation Scenario Description

Please read the [homepage](./00YashanDB Server Installation (Interactive)) to understand the interactive installation method and the deployable scenarios. 

## Installation





Please obtain the storage device configuration information in advance for specifying the --disk-found-path, --system-data, and --data parameters.

```shell
# Query the bound disk paths
$ ll /dev/yfs
```





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
    please select > 2
    ```

4. Enter the IP addresses of the servers planned for deploying the database.

   ```shell
   which ip address of the YashanDB Operation Manage, such as '192.168.1.2,192.168.1.3' or '192.168.1.[2-4]'
   please enter ip address > 192.168.1.2,192.168.1.3
   ```

5. (Optional) For multi-server deployment (multi-host), enter the installation user information as prompted:

    1. ) Enter the username, all servers' installation user usernames must be consistent.

       ```shell
       what is an SSH username used to install YashanDB
       (default: yashan)
       please enter ssh username > 
       ```

    2. ) Enter the password, all servers' installation user passwords must be consistent; if passwordless login is configured, this can be left empty.

       ```shell
       which SSH password
       please enter ssh password > ******
       ```

    3. ) Enter the SSH port number; all servers' SSH port numbers must be consistent.

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
   ```

   If the connection fails, please re-enter the server information.



### Step 2: Verify Deployment Environment

During the environment verification process, if there are no issues, the program will automatically proceed to the next step without manual operation. If any check does not meet the required environment for the database, a prompt will return `ignore it and continue, [y/n] > `, and you will need to manually enter y/n to confirm:

- n: Indicates not to ignore the environmental situation, at this point the program will exit, and you can perform the relevant commands as prompted.
- y: Indicates to ignore, and the program will continue to the next step, but the deployment may ultimately fail or functionalities may be affected.



1. Check the OS related configuration.

   ```shell
   checking system os...
   +-----------------------------------------------------------------------------------+
   | ip          | hostname    | cpu | memory  | firewalld | hugepage | YASDBA | check |
   +-----------------------------------------------------------------------------------+
   | 192.168.1.2 | host0001    | 8   | 31.26GB | inactive  | always   | joined | OK    |
   +-------------+-------------+-----+---------+-----------+----------+--------+-------+
   | 192.168.1.3 | host0002    | 8   | 31.26GB | inactive  | always   | joined | OK    |
   +-------------+-------------+-----+---------+-----------+----------+--------+-------+
   ```

   > **Note**:
   >
   > YAC Deployment will check the server name, and if the server name does not meet the requirements, it will exit directly.

2. Check the system kernel related configuration.

   ```shell
   checking system sysctl...
   +------------------------------------------------------------------------+
   | ip          | swappiness | ip_local_port_range | max_map_count | check |
   +------------------------------------------------------------------------+
   | 192.168.1.2 | 0          | 102465535           | 2000000       | OK    |
   +-------------+------------+---------------------+---------------+-------+
   | 192.168.1.3 | 0          | 3276860999          | 2000000       | OK    |
   +-------------+------------+---------------------+---------------+-------+
   ```

3. Check restrictions on system resources.

   ```shell
   checking system ulimit...
   +--------------------------------------------------------------------------------------+
   | ip          | open files | max user processes | max memory size | stack size | check |
   +--------------------------------------------------------------------------------------+
   | 192.168.1.2 | 1048576    | 65535              | unlimited       | 8192       | FAIL  |
   +-------------+------------+--------------------+-----------------+------------+-------+
   | 192.168.1.3 | 1048576    | 65535              | unlimited       | 8192       | FAIL  |
   +-------------+------------+--------------------+-----------------+------------+-------+
   
   this system deployment environment, some ulimit configure seems bad.
   please update with ulimit under root user, such as:
   echo "* soft nproc 1048576">> /etc/security/limits.conf
   echo "* hard nproc 1048576">> /etc/security/limits.conf
   
   if configure failed, please resolved by yourself, maybe need to reboot system.
   you can also ignore it and continue, but it is not friendly to the YashanDB and may affect performance or system stability
   ignore and continue [y/n] > 
   ```

4. Check the dependency libraries.

   ```shell
   checking system libraries...
   +-------------------------------------------------------------------------------------------------------------------+
   | ip          | os     | libcrypto           | libssl           | liblz4          | libz          | libzstd | check |
   +-------------------------------------------------------------------------------------------------------------------+
   | 192.168.1.2 | centos | libcrypto.so.1.1.1k | libssl.so.1.1.1k | liblz4.so.1.8.3 | libz.so.1.2.7 | unknow  | FAIL  |
   +-------------+--------+---------------------+------------------+-----------------+---------------+---------+-------+
   | 192.168.1.3 | centos | libcrypto.so.1.1.1k | libssl.so.1.1.1k | liblz4.so.1.8.3 | libz.so.1.2.7 | unknow  | FAIL  |
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



2. Set the primary/standby scale, that is, input the total number of primary/standby clusters.

   For single-cluster deployment, enter 1; for one-primary/one-standby, enter 2, and so on.

    ```shell
    how many groups are there in the YashanDB YAC
    (default: 1)
    please enter the number of group >
    ```

3. Input the number of nodes in the cluster.

   - Primary Cluster

     ```shell
     how many nodes are there in the primary cluster
     (default: 2)
     please enter the number of nodes > 2
     ```

   - Standby Cluster (only need to be set in primary/standby cluster deployment)

     ```shell
     how many nodes are there in a standby cluster(all standby clusters have the same nodes)
     (default: 2)
     please enter the number of nodes > 2
     ```

4. Input the number of failure groups for YFS disks, which should be consistent across all cluster groups.

   ```shell
   how many failuregroups are there in a cluster
   (default: 1)
   please enter the number of failuregroups > 1
   ```

5. Configure shared storage information.

   If deploying a primary/standby YAC, multiple sets of storage device information are required.

   ```shell
   please configure the disk for the cluster ceg1
   ```

   - Disk found path:

     ```shell
     what is the disk found path, you can input string like 'path1,path2,path3' if there are several paths
     please enter disk found path > /dev/yfs
     ```

   - System disks:

     ```shell
     what is the disk of system diskgroup, you can input string like 'disk1,disk2,disk3' if there are several disks
     please enter system data > /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2
     ```

   - Data disks:

     ```shell
     what is the disk path of data, you can input string like 'disk1,disk2,disk3' if there are several disks
     please enter data disk > /dev/yfs/data0
     ```

6. Configure network resource information:

    To configure VIP or SCAN, the public network segment must also be configured simultaneously, and the VIP must belong to the subnet corresponding to the public network.

    - VIP configuration information for YAC, in the format IP_address/subnet_mask[/network_interface_name], for example: 192.168.1.62/255.255.255.0/ens192. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted. --vips must be used with --public-network parameter, and the IP address should belong to the public network. The number of VIPs should match the number of nodes, and multiple VIP configurations are separated by commas `,`

      ```shell
      what is the vips, if you need, you can input string like 'ip1,ip2,ip3'
      please enter vips > 192.168.1.62/24,192.168.1.63/24
      ```

    - Public network subnet of YAC, in format subnet/subnet_mask[/network_interface_name], where the network interface name is optional, for example: 192.168.1.0/24

      ```shell
      what is the public network, if you need, you can input string
      please enter public network > 192.168.1.0/24/ens192
      ```

    - SCAN domain name of YAC, must be used with --public-network parameter

      ```shell
      what is the scanname, if you need, you can input string
      please enter scan name > scan.example.com
      ```

7. Detect shared storage and other devices/environments to verify whether [I/O Fencing based on SCSI persistent reservation](../Pre-Installation Preparation/Preparing the Servers) can be used.

    Even if detection errors occur, they will not block installation and deployment, but the I/O Fencing method for YAC will be configured to use the in-flight I/O protection algorithm. If you still need to use I/O Fencing based on SCSI persistent reservations, please make adjustments according to the corresponding error prompts.

    ```shell
    If you want to use SCSI I/O Fencing, please make sure user of host has  sudo permission with no password
    Now check if SCSI I/O Fencing can be selected......
    
    SCSI-based I/O Fencing Test(Dual-host) Start
    Test Running on /dev/yfs/datadisk...
    Device /dev/yfs/datadisk Support SCSI-based I/O Fencing
    Supportive Device: /dev/yfs/datadisk
    Test succeed
    Cluster will be configured with SCSI-based I/O Fencing
    ```

8. Select the plugins to install, using commas `,` to separate multiple values.

    |Option |Description                                   |
    | --------------- | ------------------------------------------------------ |
    | 1/none          | Do not install plugins                           |
    | 2/all           | Install all plugins, default value                            |
    | 3/gis           | Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions/GIS Function/00GIS Function) function                            |
    | 4/dblink        | Install plugins for DBLink related functionality                         |
    | 5/udf           | Install plugins for [ UDF ](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality                            |
    | 6/s3            | Install plugins for S3 bucket related functionality (corresponding functionality is not applicable to YAC/Distributed Cluster Deployment, no need to specify installation of this plugin separately)      |
    | 7/listagg       | Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions/LSFA_LISTAGG) function (corresponding functionality is not applicable to YAC/Distributed Cluster Deployment, no need to specify installation of this plugin separately) |
    | 8/xml           | Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions/XML Function/00XML Function) function                            |

    

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

4. Configure the required directories for the database based on the [directory partitioning](../Pre-Installation Preparation/Creating Users and Directories) plan:

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
    1) manage-ip: listen on 192.168.1.2,192.168.1.3
    2) 0.0.0.0: listen on 0.0.0.0
    (default: 1)
    please select > 
    ```

9. Set the initial password for the super administrator sys user with the following requirements:

    - Password length should be 8 - 64 characters.

    - The password must not include the corresponding database username.

    - The password must contain numbers, letters, and special characters.

    - OS command-related special characters (such as `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped.

    ```shell
    which sys password for YashanDB
    please enter sys password > ********
    checking sys password now, please wait...
    ```

    
9. Configure the starting port.

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

    This operation only configures the database to start automatically. If [SCSI persistent reservation I/O Fencing functionality](../../../Database Administration/Cluster Management/IO Fencing/SCSI IO Fencing) has been enabled, the startup of the *ycsrootagent* process should also be configured. Please refer to [Configure Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart) for steps.
    
    

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
  

- Configure the $YASCS_HOME environment variable.

   This environment variable needs to be configured for each server in the current environment.

   ```shell
   # Based on the installation example instance 1-1 from the previous steps, the path example value is as follow, but the actual node paths should be used as the standard

   $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1
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

cluster deploy failed, stdout:task completed, status: FAILED
retcode: 1
stdout: create diskgroup DG0 failed, you can try to set yfs_force_create to true
stderr:
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
  hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address     | data_path                                          
  --------------------------------------------------------------------------------------------------------------------------------------------------------------
  host0001 | ce        | 1-1:1  | 19513 | open            | normal          | primary       | 192.168.1.2:1688   | /data/yashan/yasdb_data/ce-1-1 
  ----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+---------------------------------------------
  host0002 | ce        | 1-2:2  | 19608 | open            | normal          | primary       | 192.168.1.3:1688   | /data/yashan/yasdb_data/ce-1-2 
  ----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+---------------------------------------------
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

