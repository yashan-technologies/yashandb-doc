Standalone (primary-standby) deployment includes the following specific deployment forms and scenarios: 

|Deployment Forms                                |Deployment Scenarios                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Single node<br>One primary, one standby<br>One primary, multiple standbys<br>Cascadedstandbys<br>Dual replication group primary-standby | yashan mode / mysql mode<br>Single network segment / multiple network segments |



The example installation process described in the [second part](#installation) of this document will be demonstrated by default under the scenario of yashan mode, and public and private networks located in the same network segment. Please carefully read the explanation of various installation scenarios in the [first part](#InstallationScenario) of this document, and refer to the guidance to adjust the example commands before running the installation. 

<span id="InstallationScenario" name="InstallationScenario"></span>

## Installation Scenario Description

Please confirm your installation scenario and enter the corresponding chapter to view. 



### mysql Mode

YashanDB deployed in mysql mode will run in a syntax mode highly compatible with MySQL. After installation, it requires using MySQL syntax format rather than YashanDB syntax format to operate and use the database. 

If you decide to use this mode for installation, you need to add the **--mode mysql** option to the corresponding command in [Step 1: Generate Configuration File] of the example installation process, for example: 

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--mode mysql
```

Subsequent steps are identical. 

After specifying the **--mode mysql** option, the relevant changes in the generated configuration file are as follows (The yashandb.toml file name comes from the cluster name specified in the sample installation command. If you specify a different name, the file name here will accordingly be different.): 

```toml
...
mode = "MYSQL"          # The database server syntax mode is set to MYSQL.
...
table_type = "HEAP"     # Main business table types, mysql mode can only be HEAP tables.
...

[[group]]
  ...
  [group.config]        
    ...

  [[group.node]]
    ...
    mysql_addr = "192.168.1.2:1690" # MySQL protocol listening address, this parameter exists only in mysql mode.
    ...
    [group.node.config] 
      ...
      EMPTY_STRING_AS_NULL = false  # Database configuration parameters, empty string not equal to NULL indicates mysql mode, manual modification is not allowed.
      ...
    [group.node.mysql_config] # All global variables under mysql mode can be configured under this item.
```



### Multiple Network Segments

Please refer to the [network planning](../Pre-Installation Preparation/Preparing the Networks) documentation to understand o ur definition and division recommendations for YashanDB public and private networks. 

If you have assigned public and private networks to different physical network segments during network preparation (i.e., a server may have multiple IP addresses in different network segments), this may affect the selection of values for the following installation parameters in the corresponding command of [Step 1: Generate Configuration File] in the example installation process: 

- **--ip**: This parameter value is the installation login server IP address, and also the default external database listening address. Please set it to the public IP address. 
- **--listen-cidr**: The subnet where the database listening address is located. Generally, no input is required as the default value is used, which means using the subnet of the IP address entered with --ip. 

- **--replica-cidr**: This parameter value is the network segment where the primary-standby replication link address is located, which can be set to the private network segment in multi-network segment scenarios to generate private network addresses for the primary-standby replication link.

    When the primary-standby replication addresses of primary and standby nodes are located in different network segments (for example, remote cascade standby or local dual replication group primary-standby), this option cannot be used. Instead, the addresses should be specified separately by modifying the configuration file as follows (the yashandb.toml file name comes from the cluster name specified in the example installation command. If you specified a different name, the file name here will be correspondingly different):

    ```toml
    ## Clear the public network address in the REPLICATION_ADDR parameter, and add the following information at the bottom of the configuration file. Note that the hostid should match the IP address.
    ...
        REPLICATION_ADDR = ":1689"
    ...
        REPLICATION_ADDR = ":1689"
    ...
    [[host]]
    hostid = "host0001"
    [host.yasagent]
    [host.yasdb_ip]
        replica_ip = "172.16.1.2"

    [[host]]
    hostid = "host0002"
    [host.yasagent]
    [host.yasdb_ip]
        replica_ip = "172.16.1.3"
    ```

Subsequent steps are identical. 

<span id="installation" name="installation"></span>

## Single Node Deployment

Deploying a single-node YashanDB will create an independent database instance on a specified server. 


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```


### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package se gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.
    
    :::tabs
    == Default Example Scenario

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 
```

    == mysql Mode

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--mode mysql
```
    :::

    

    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Please confirm whether the configuration file generation command you entered in the previous step corresponds to your required [installation scenario](#InstallationScenario).

    If you need to modify the scenario, you can adjust the configuration file generation command entered in the previous step according to the operational guidance of the corresponding scenario, rerun it, and generate the configuration file again.

3. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml

​

|Parameter Item |Adjustment Description                          |
| ------------------------- | ------------------------------------------------------------ |
| All LISTEN_ADDR           | The address and listening port where the yasom and yasagent service is located. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |



    == yashandb.toml

|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| table_type                                 | Main business table type, default HEAP, cannot be adjusted in mysql mode |
| recommend_param/scale_factor/scene         | When the --recommend-param option is specified, the recommended parameter options can be further adjusted. For details, please refer to the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package documentation. |
| mysql_addr                                 | This parameter does not exist in yashan mode<br>MySQL protocol listening address and port. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.mysql_config] | These parameters are all [system variables](../../../Reference Manual of mysql Mode/System Variables/List of System Variables In mysql Mode) under mysql mode (ignore in yashan mode). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |

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


### Step 5: Check Installation Result


If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    An example of the echoed information is as follows: 

    :::tabs
    == Default Example Scenario

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    == mysql Mode

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    :::



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



## One Primary, One Standby Deployment

Deploying a one primary, one standby YashanDB will create database instances on two server nodes, respectively as primary and standby roles, providing read-write (primary) and read-only (standby) services externally.


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```


### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package se gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.

    Please note that the order of addresses entered after --ip determines the default primary-standby roles, and the system uses the first IP address entered as the primary database node.

    :::tabs
    == Default Example Scenario  

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2
```

    == mysql Mode

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2 \
--mode mysql
```

    == Multiple Network Segments

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2  \
--replica-cidr 172.16.1.0/24
```
    :::



    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Please confirm whether the configuration file generation command you entered in the previous step corresponds to your required [installation scenario](#InstallationScenario).

    If you need to modify the scenario, you can adjust the configuration file generation command entered in the previous step according to the operational guidance of the corresponding scenario, rerun it, and generate the configuration file again.

3. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml

|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| hostid and LISTEN_ADDR under [om]  | yasom service host identifier, IP address and listening port<br>Please check to ensure the listening port number is consistent with the actually opened port number, and can be modified to the actual value |
| All LISTEN_ADDR under [host.yasagent]     | yasagent service IP address and listening port<br>Please check to ensure the listening port number is consistent with the actually opened port number, and can be modified to the actual value            |

    == yashandb.toml

​

|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| table_type                                 | Main business table type, default HEAP, cannot be adjusted in mysql mode |
| recommend_param/scale_factor/scene         | When the --recommend-param option is specified, the recommended parameter options can be further adjusted. For details, please refer to the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package documentation. |
| data_path                                  | The value corresponding to the YAS_DATA environment variable. By default, the data paths of all nodes are identical, and adjustment is not recommended.<br>If the data paths of the primary-standby nodes are inconsistent, then the values of the DB_FILE_NAME_CONVERT, REDO_FILE_NAME_CONVERT, and DB_BUCKET_NAME_CONVERT parameters must be adjusted simultaneously on all nodes. |
| mysql_addr                                 | This parameter does not exist in yashan mode<br>MySQL protocol listening address and port. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| role                                       | This parameter defines the primary-standby role of the node instance. By default, the first value entered in the --ip option is the primary database. If inconsistency with the actual situation is found, it is recommended to re-specify the --ip option and run the configuration file generation command. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.mysql_config] | These parameters are all [system variables](../../../Reference Manual of mysql Mode/System Variables/List of System Variables In mysql Mode) under mysql mode (ignore in yashan mode). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| REPLICATION_ADDR                           | This parameter defines the address and port of the primary-standby replication link.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |

​

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

1. Execute the following commands to activate the environment variables.

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


2. Log in to other server(s) in the current environment as the installation user and repeat the above operations to make the environment variables take effect.


### Step 5: Check Installation Result


If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    An example of the echoed information is as follows: 

    The following output is completely consistent in multi-segment network scenarios.

    :::tabs
    == Default Example Scenario 

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0002 | db        | 1-2:2  | 31194 | open            | normal          | standby       | 192.168.1.3:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    == mysql Mode

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0002 | db        | 1-2:2  | 31194 | open            | normal          | standby       | 192.168.1.3:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    :::



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



### Step 7: Enable Leader Election (Optional)

In a one primary, one standby deployment form, if you need to enable leader election, please refer to the operational instructions described in the [Configuring yasom Election for One-Primary and One-Standby](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby) document for configuration.

## One-Primary and Multi-Standby Deployment

Deploying a one-primary and multi-standby YashanDB will create database instances on multiple server nodes, respectively as primary and standby roles, providing read-write (primary) and read-only (standby) services externally.


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```


### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package se gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.

    Please note that the order of addresses entered after --ip determines the default primary-standby roles, and the system uses the first IP address entered as the primary database node. Multiple IP addresses are entered one by one, separated by commas. For consecutive IP addresses, range format can be used for input, for example, 192.168.1.[2-4].

    The following command uses one master and two standbys as an example. Please adjust the parameters according to the actual situation.

    :::tabs
    == Default Example Scenario  

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 3
```

    == mysql Mode

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 3 \
--mode mysql
```

    == Multiple Network Segments

```shell
$ ./bin/yasboot package se gen --cluster yashandb --recommend-param \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.1.4 --port 22 \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 3 \
--replica-cidr 172.16.1.0/24
```
    :::



    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Please confirm whether the configuration file generation command you entered in the previous step corresponds to your required [installation scenario](#InstallationScenario).

    If you need to modify the scenario, you can adjust the configuration file generation command entered in the previous step according to the operational guidance of the corresponding scenario, rerun it, and generate the configuration file again.

3. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml



|Parameter Item |Adjustment Description                          |
| ------------------------- | ------------------------------------------------------------ |
| All LISTEN_ADDR           | The address and listening port where the yasom and yasagent service is located. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |



    == yashandb.toml



|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| table_type                                 | Main business table type, default HEAP, cannot be adjusted in mysql mode |
| recommend_param/scale_factor/scene         | When the --recommend-param option is specified, the recommended parameter options can be further adjusted. For details, please refer to the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package documentation. |
| data_path                                  | The value corresponding to the YAS_DATA environment variable. By default, the data paths of all nodes are identical, and adjustment is not recommended.<br>If the data paths of the primary-standby nodes are inconsistent, then the values of the DB_FILE_NAME_CONVERT, REDO_FILE_NAME_CONVERT, and DB_BUCKET_NAME_CONVERT parameters must be adjusted simultaneously on all nodes. |
| mysql_addr                                 | This parameter does not exist in yashan mode<br>MySQL protocol listening address and port. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| role                                       | This parameter defines the primary-standby role of the node instance. By default, the first value entered in the --ip option is the primary database. If inconsistency with the actual situation is found, it is recommended to re-specify the --ip option and run the configuration file generation command. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.mysql_config] | These parameters are all [system variables](../../../Reference Manual of mysql Mode/System Variables/List of System Variables In mysql Mode) under mysql mode (ignore in yashan mode). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| REPLICATION_ADDR                           | This parameter defines the address and port of the primary-standby replication link.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |

​

> **Note**:
>
> The HA_ELECTION_ENABLED parameter is the main switch for the leader election function, which cannot be modified directly here. When deploying one-primary and multi-standby, this parameter is automatically and forcibly enabled. If you need to disable it, you can only do so manually after installation by referring to the [Configuring Leader Election for One-Primary and Multi-Standby](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby) documentation.

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

1. Execute the following commands to activate the environment variables.

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


2. Log in to other server(s) in the current environment as the installation user and repeat the above operations to make the environment variables take effect.


### Step 5: Check Installation Result


If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    An example of the echoed information is as follows: 

    The following output is completely consistent in multi-segment network scenarios.

    :::tabs
    == Default Example Scenario

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0002 | db        | 1-2:2  | 31194 | open            | normal          | standby       | 192.168.1.3:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0003 | db        | 1-3:3  | 13384 | open            | normal          | standby       | 192.168.1.4:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-3 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    == mysql Mode

```shell
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 25283 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/db-1-1 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0002 | db        | 1-2:2  | 31194 | open            | normal          | standby       | 192.168.1.3:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-2 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
| host0003 | db        | 1-3:3  | 13384 | open            | normal          | standby       | 192.168.1.4:1688  | 1-1:1       | /data/yashan/yasdb_data/db-1-3 |
+----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
```
    :::



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



## Cascade Standby Deployment

When deploying a primary-standby form with cascade standby, the cascade standby and the standby database it connects to form a new layer of cascade standby relationship, but both are read-only databases.

Cascade standbys can be established for different standby databases, as well as multi-layer cascade standbys. However, during the installation and deployment process, only one layer of cascade standby deployment can be performed for one standby database. After completion, other cascade standbys can be added to this database cluster by referring to the operational instructions described in the [Cascading Standbys Scaling](../../Scalability/Scaling for Standalone Deployment/Cascading Standbys Scaling).


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```


### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package se gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.

    Please note that the values following the --ip option should be entered in the order of primary, standby, and cascade standby. Multiple IP addresses are entered one by one, separated by commas. For consecutive IP addresses, range format can be used for input, for example, 192.168.1.[2-4].

    In addition, the value of the --node option will be used as the number of primaries plus standbys, the value of the --cascade-node option will be used as the number of cascade standbys, and the value of the --cascade-parent option is used to locate the parent standby database of the cascade standby. This value represents the index starting from 0 of the IP addresses entered in the --ip option.

    The following command uses one primary, one standby, and one cascade standby as an example. Please adjust the parameters according to the actual situation.

    :::tabs
    == Default Example Scenario

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.[2-4] --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2 \
--cascade-node 1 --cascade-parent 1
```

    == mysql Mode

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.[2-4] --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2 \
--cascade-node 1 --cascade-parent 1 \
--mode mysql
```

    == Multiple Network Segments

When the primary-standby-cascade standby replication link addresses are in the same network segment, use --replica-cidr to specify the network segment; Otherwise, manual adjustments should be made in the configuration file.

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.[2-4] --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--node 2 \
--cascade-node 1 --cascade-parent 1 \
--replica-cidr 172.16.1.0/24
```
    :::



    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Please confirm whether the configuration file generation command you entered in the previous step corresponds to your required [installation scenario](#InstallationScenario).

    If you need to modify the scenario, you can adjust the configuration file generation command entered in the previous step according to the operational guidance of the corresponding scenario, rerun it, and generate the configuration file again.

3. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml



|Parameter Item |Adjustment Description                          |
| ------------------------- | ------------------------------------------------------------ |
| All LISTEN_ADDR           | The address and listening port where the yasom and yasagent service is located. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |



    == yashandb.toml


|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| table_type                                 | Main business table type, default HEAP, cannot be adjusted in mysql mode |
| recommend_param/scale_factor/scene         | When the --recommend-param option is specified, the recommended parameter options can be further adjusted. For details, please refer to the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package documentation. |
| data_path                                  | The value corresponding to the YAS_DATA environment variable. By default, the data paths of all nodes are identical, and adjustment is not recommended.<br>If the data paths of the primary-standby nodes are inconsistent, then the values of the DB_FILE_NAME_CONVERT, REDO_FILE_NAME_CONVERT, and DB_BUCKET_NAME_CONVERT parameters must be adjusted simultaneously on all nodes. |
| mysql_addr                                 | This parameter does not exist in yashan mode<br>MySQL protocol listening address and port. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| role                                       | This parameter defines the primary-standby and cascade standby roles of the node instances. If inconsistency with the actual situation is found, it is recommended to re-specify the --ip, --node, --cascade-node, and --cascade-parent options and run the configuration file generation command again. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.mysql_config] | These parameters are all [system variables](../../../Reference Manual of mysql Mode/System Variables/List of System Variables In mysql Mode) under mysql mode (ignore in yashan mode). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| REPLICATION_ADDR                           | This parameter defines the address and port of the primary-standby replication link.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |
| Parameters under [[host]]                  | When --replica-cidr is not specified, this type of parameter will not be generated in the configuration file.<br>After specifying the private IP address for each node, this type of parameter can be manually added, or adjustments can be made to the already generated parameters. |

​

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

1. Execute the following commands to activate the environment variables.

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


2. Log in to other server(s) in the current environment as the installation user and repeat the above operations to make the environment variables take effect.


### Step 5: Check Installation Result


If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    An example of the echoed information is as follows: 

    The following output is completely consistent in multi-segment network scenarios.

    :::tabs
    == Default Example Scenario

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
    == mysql Mode

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
    :::



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



## Dual Repli-Group Primary-Standby Deployment

Dual repli-group primary-standby deployment divides the database cluster into a primary replication group and a standby replication group, with each group's corresponding servers installed in different regions/data centers.The primary replication group should provide 3 or more nodes, deployed in a one-primary and multi-standby form; the standby replication group should provide 2 or more nodes, deployed in a topology of one standby database with cascade standbys.


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```


### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package se gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.
    Please note that the values following the --ip option should be entered in the order of primary replication group primary, primary replication group standby, standby replication group standby, and standby replication group cascade standby. Multiple IP addresses are entered one by one, separated by commas. For consecutive IP addresses, range format can be used for input, for example, 192.168.1.[2-4].

    In addition, for dual repli-group primary-standby deployment, the --group option value should be entered as 2, the --node option value will be used as the number of primary plus standby in the primary replication group, and the --standby-node option value will be used as the number of standby plus cascade standby in the standby replication group.

    The following command uses one primary and two standbys in the primary replication group, and one standby with three cascade standbys in the standby replication group as an example. Please adjust the parameters according to the actual situation.

    :::tabs
    == Default Example Scenario

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

    == mysql Mode

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password  \
--ip 192.168.1.2,192.168.1.3,192.168.1.4,192.168.10.5,192.168.10.6,192.168.10.7 --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--group 2 --node 3 --standby-node 3 \
--mode mysql
```

    == Multiple Network Segments

The following command is only applicable when all primary-standby replication link addresses are located in the same physical network segment; otherwise, manual adjustments should be made in the configuration file.

```shell
$ ./bin/yasboot package se gen --cluster yashandb \
-u yashan -p yashan_password  \
--ip 192.168.1.2,192.168.1.3,192.168.1.4,192.168.10.5,192.168.10.6,192.168.10.7 --port 22  \
--install-path /data/yashan/yasdb_home  \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--group 2 --node 3 --standby-node 3 \
--replica-cidr 172.16.1.0/24
```
    :::



    After completion, the current directory will generate two configuration files: hosts.toml and yashandb.toml.

2. Please confirm whether the configuration file generation command you entered in the previous step corresponds to your required [installation scenario](#InstallationScenario).

    If you need to modify the scenario, you can adjust the configuration file generation command entered in the previous step according to the operational guidance of the corresponding scenario, rerun it, and generate the configuration file again.

3. Manually modify the relevant parameters in the configuration file according to the actual needs.

    >**Caution**:
    >
    > It is not recommended to delete any lines in the file, as this may lead to errors during subsequent installation processes or make the built environment unable to be expanded for configuration later.

    The following lists some parameter items that may need adjustment. For complete configuration file content descriptions, please refer to the configuration file template introduction. Please select parameter items to adjust values according to the actual situation. 

    :::tabs
    == hosts.toml



|Parameter Item |Adjustment Description                          |
| ------------------------- | ------------------------------------------------------------ |
| All LISTEN_ADDR           | The address and listening port where the yasom and yasagent service is located. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |



    == yashandb.toml


|Parameter Item                  |Adjustment Description                          |
| ------------------------------------------ | ------------------------------------------------------------ |
| table_type                                 | Main business table type, default HEAP, cannot be adjusted in mysql mode |
| recommend_param/scale_factor/scene         | When the --recommend-param option is specified, the recommended parameter options can be further adjusted. For details, please refer to the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package documentation. |
| data_path                                  | The value corresponding to the YAS_DATA environment variable. By default, the data paths of all nodes are identical, and adjustment is not recommended.<br>If the data paths of the primary-standby nodes are inconsistent, then the values of the DB_FILE_NAME_CONVERT, REDO_FILE_NAME_CONVERT, and DB_BUCKET_NAME_CONVERT parameters must be adjusted simultaneously on all nodes. |
| mysql_addr                                 | This parameter does not exist in yashan mode<br>MySQL protocol listening address and port. Please check to ensure it is consistent with the actually opened port number, and can be modified to the actual value. |
| role                                       | This parameter defines the primary-standby and cascade standby roles of the node instances. If inconsistency with the actual situation is found, it is recommended to re-specify the --ip, --node, --cascade-node, and --cascade-parent options and run the configuration file generation command again. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.node.mysql_config] | These parameters are all [system variables](../../../Reference Manual of mysql Mode/System Variables/List of System Variables In mysql Mode) under mysql mode (ignore in yashan mode). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| REPLICATION_ADDR                           | This parameter defines the address and port of the primary-standby replication link.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |
| Parameters under [[host]]                  | When --replica-cidr is not specified, this type of parameter will not be generated in the configuration file.<br>After specifying the private IP address for each node, this type of parameter can be manually added, or adjustments can be made to the already generated parameters. |

​

> **Note**:
> 
> The HA_ELECTION_ENABLED parameter is the main switch for the leader election function, which cannot be modified directly here. This parameter for each node in the primary replication group is automatically and forcibly enabled. If you need to disable it, you can only do so manually after installation by referring to the [Configuring Leader Election for One-Primary and Multi-Standby](../../../High Availability/Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby) documentation.
> 
> The standby replication group does not participate in leader election, and all nodes must disable the leader election function. Please keep the HA_ELECTION_ENABLED parameter at its default value FALSE.

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

1. Execute the following commands to activate the environment variables.

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


2. Log in to other server(s) in the current environment as the installation user and repeat the above operations to make the environment variables take effect.


### Step 5: Check Installation Result


If there are connection errors or SQL statement execution errors, please check the installation steps according to the error message or consult our technical support.

1. Execute the following command to check the database status.

    ```shell
    $ yasboot cluster status -c yashandb -d
    ```

    An example of the echoed information is as follows: 

    The following output is completely consistent in multi-segment network scenarios.

    :::tabs
    == Default Example Scenario 

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
    == mysql Mode

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


