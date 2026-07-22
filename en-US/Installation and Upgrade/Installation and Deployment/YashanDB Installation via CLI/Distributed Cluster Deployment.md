Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```

Please obtain the storage device configuration information in advance for specifying the --disk-found-path, --system-data, and --data parameters. And make software link to NVMe disks.

```shell
# Query the bound disk paths (i.e., the soft links of NVMe-oF disks)
$ ll /dev/yfs
```

### Step 1: Generate Deployment File

This step generates two important configuration files for the YashanDB product: hosts.toml and {cluster_name}.toml, where the cluster name comes from the parameter value of the --cluster option. In this document, it is yashandb, and this configuration file is referred to as yashandb.toml throughout the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package ce gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation. 

    ```shell
    $ ./bin/yasboot package ce gen -c yashandb \
      -u yashan -p yashan \
      --cn-ip 192.168.1.2,192.168.1.3 \
      --dn-ip 192.168.1.4,192.168.1.5 \
      --install-path /data/yashan/yasdb_home \
      --data-path /data/yashan/yasdb_data \
      --log-path /data/yashan/yasdb_home/log \
      --data /dev/data0 \
      --disk-found-path /dev \
      --system-data /dev/sys0 \
      --connect-param \ -i\ 4\ -Q\ 32\ -l\ -1
    ```

    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml

|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| All LISTEN_ADDR                    | The address and listening port where the yasom and yasagent service is located. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| dataDisks                           | Data disk, can be modified to actual value.             |
| systemDisks                           | System disk, can be modified to actual value.              |
| The [host.nvmeof] block of the DN servers                         | The NVMe-oF disk configuration on the DN server, such as `port` indicating the NVMe-oF listening port number, should be checked to ensure it matches the actual value; you can modify it to the real value if needed.             |

    == yashandb.toml

|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [[group.diskgroup]]            | These parameters are all disk group configurations for data disks and can be manually adjusted. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.systemdiskgroup]            | These parameters are all system disk group configurations and can be manually adjusted.<br>System disk configuration cannot be adjusted after installation. |
| Parameters under [group.ycsconfig]            | These parameters are all [YCS configuration parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration) and can be manually adjusted. |
| Parameters under [group.yfsconfig]            | These parameters are all [YFS configuration parameters](../../../Database Administration/Storage Management/YFS Management/YFS Parameter Configuration) and can be manually adjusted. |
| CLUSTER_INTERCONNECT    | This parameter defines the communication address and port between cluster instances.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |
| INTER_URL    | This parameter defines the communication address and port between YCS instances.<br>The port number defaults to the port number of LISTEN_ADDR + 100. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1788". |

    :::

### Step 2: Execute Installation

Execute the following command to perform the software package installation.

```shell
$ ./bin/yasboot package install -t hosts.toml
```

|Parameter |Description |
|--------------------|--------------------------------------|
| -t, --toml | Path to the hosts.toml file for deploying the database cluster |

### Step 3: Database Deployment

1. Execute the following command to deploy the database:

    ```shell
    $ ./bin/yasboot cluster deploy -t yashandb.toml --yfs-force-create -p sys_password
    ```

    |Parameter |Description |
    |--------------------|--------------------------------------|
    | -t, --toml         | Path to the toml file for deploying the database cluster |
    | --yfs-force-create  | Force YFS to create disk group         |
    | -p, --sys-password | Set the password for the database superuser sys. The configuration requirements are as follows:<br/>* Password length must be between 8 - 64 characters<br/>* Password must not contain the corresponding database username<br/>* Password must contain numbers, letters, and special characters<br/>* Special characters related to OS commands (e.g., `@`, `/`, `.`, `!`, `$`, `'`, etc.) must be escaped  |

    > **Note**:
    >
    > You can specify the `-d, --child` parameter to display the task and sub-task information to understand the deployment progress.
    
    The following output indicates the deployment has succeeded.
    ```shell
    task completed, status: SUCCESS
    ```

2. Execute the following command to restart the database:

    ```shell
    $ ./bin/yasboot cluster restart -c yashandb
    ```

### Step 4: Configure Environment Variables

After successful deployment, the directory specified by --install-path (for example, /data/yashan/yasdb_home) will generate a subdirectory `{version_number}/conf`, and this directory will automatically generate a YashanDB-related environment variable file named `{cluster_name}.bashrc`, which needs to be applied to the OS.

1. Execute the following commands to activate the environment variables.

    ```shell
    # Enter the directory where the environment variable file is located, e.g. /data/yashan/yasdb_home/{version_number}/conf
    $ cd /data/yashan/yasdb_home/{version_number}/conf

    # Activate environment variables
    $ cat yashandb.bashrc >> ~/.bashrc
    $ source ~/.bashrc
    
    # Verify if Environment Variables Are Effective (Please use actual paths from the echo output)
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/ce-1-1
    ```

    For detailed information about environment variables, please refer to [Initial Environment After Installation > Environment Variables](../Initial Environment after Installation/Environment Variables).

2. Execute the following command to configure the $YASCS_HOME environment variable.

    ```shell
    # Based on the installation example instance 1-1 from the previous text, the path example values are as follows, but the node paths should be based on actual values
    $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1

    # Verify if Environment Variables Are Effective (Please use actual paths from the echo output)
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/ce-1-1
    ```

3. Log in to other server(s) in the current environment as the installation user and repeat the above operations to configure the environment variables.

### Step 5: Check Installation Result

If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```
        
    An example of the echoed information is as follows: 

    ```shell
    +------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid  | pid   | instance_status | database_status | database_role | listen_address     | source_node | data_path                              |
    +------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | cn        | 1-1:1 | 46667   | open            | normal          | primary       | 192.168.1.2:1688    | -           | /data/yashan/yasdb_data/ce-1-1         |
    +----------+-----------+---------+-------+-----------------+-----------------+---------------+--------------------+-------------+----------------------------------------+
    | host0001 | dn        | 2-1:1 | 42742   | open            | normal          | primary       |  yasfs.ipc         | -           | /data/yashan/yasdb_data/ycs/ce-1-1     |
    +----------+-----------+---------+-------+-----------------+-----------------+---------------+--------------------+-------------+----------------------------------------+
    | host0002 | cn        | 1-1:2 | 36372   | open            | normal          | primary       | 192.168.1.3:1688    | -           | /data/yashan/yasdb_data/ce-1-2         |
    +----------+-----------+---------+-------+-----------------+-----------------+---------------+--------------------+-------------+----------------------------------------+
    | host0002 | dn        | 2-1:2 | 37441   | open            | normal          | primary       |  yasfs.ipc         | -           | /data/yashan/yasdb_data/ycs/ce-1-2     |
    +----------+-----------+---------+-------+-----------------+-----------------+---------------+--------------------+-------------+----------------------------------------+
    ```
 

2. Use the [yasql](../../../Tools Guide/yasql/User Guide for yasql) tool to connect to the database and check the instance status.

    ```shell
    $ yasql sys/********@192.168.1.2:1688
    SQL> SELECT STATUS FROM v$instance;

    STATUS        
    ------------- 
    OPEN        

    SQL> SELECT database_name FROM v$database;

    DATABASE_NAME                                                    
    ---------------------------------------------------------------- 
    yashandb   
    ```

### Step 6: Start Daemon Processes

Execute the following command to start the daemon processes.

```shell
$ yasboot monit start --cluster yashandb
```

> **Note**:
>
> In subsequent usage, after the server reboots, the daemon processes will need to be manually started. The daemon will automatically bring up related database processes to start the database. To simplify operations, you may configure the daemon to start automatically on boot, indirectly achieving automatic database startup; for specific operations, please refer to [Configuring Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart).