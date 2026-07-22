YAC deployment includes the following specific deployment forms and scenarios: 

|Deployment Forms             |Deployment Scenarios                            |
| ----------------------------------------- | ------------------------------------------------------------ |
| Single cluster<br>primary-standby cluster |Single network segment / multiple network segments<br>SCAN and VIP |



The example installation process described in the [second part](#installation) of this document will be demonstrated by default under the scenario of public and private networks located in the same network segment. Please carefully read the explanation of various installation scenarios in the [first part](#InstallationScenario) of this document, and refer to the guidance to adjust the example commands before running the installation. 

<span id="InstallationScenario" name="InstallationScenario"></span>

## Installation Scenario Description

Please confirm your installation scenario and enter the corresponding chapter to view. 





### Multiple Network Segments

Please refer to the [network planning](../Pre-Installation Preparation/Preparing the Networks) documentation to understand o ur definition and division recommendations for YashanDB public and private networks. 

If you have assigned public and private networks to different physical network segments during network preparation (i.e., a server may have multiple IP addresses in different network segments), this may affect the selection of values for the following installation parameters in the corresponding command of [Step 1: Generate Configuration File] in the example installation process: 

- **--ip**: This parameter value is the installation login server IP address, and also the default external database listening address. Please set it to the public IP address. 
- **--listen-cidr**: The subnet where the database listening address is located. Generally, no input is required as the default value is used, which means using the subnet of the IP address entered with --ip. 

- **--inter-cidr**: This parameter value is the network segment where the cluster instance communication address is located, which can be set to the private network segment in multi-network segment scenarios to generate private network addresses.
- **--replica-cidr**: This parameter value is the network segment where the primary-standby replication link address is located, which can be set to the private network segment in multi-network segment scenarios to generate private network addresses.

    When the primary-standby replication addresses of the primary-standby clusters are located in different network segments, this option cannot be used. Instead, the addresses should be specified separately by modifying the configuration file as follows (the yashandb.toml file name comes from the cluster name specified in the example installation command. If you specified a different name, the file name here will be correspondingly different):

    ```toml
    ## Clear the public network address in the REPLICATION_ADDR and CLUSTER_INTERCONNECT parameters, and add the following information at the bottom of the configuration file. Note that the hostid should match the IP address.
    ...
        CLUSTER_INTERCONNECT = ":1689"
    ...
        REPLICATION_ADDR = ":1690"
    ...
        CLUSTER_INTERCONNECT = ":1689"
    ...
        REPLICATION_ADDR = ":1690"
    ...
    [[host]]
    hostid = "host0001"
    [host.yasagent]
    [host.yasdb_ip]
        inter_ip = "172.16.1.2"
        inter_url = "172.16.1.2"
        replica_ip = "172.16.1.2"

    [[host]]
    hostid = "host0002"
    [host.yasagent]
    [host.yasdb_ip]
        inter_ip = "172.16.1.3"
        inter_url = "172.16.1.3"
        replica_ip = "172.16.1.3"
    ```

Subsequent steps are identical. 

<span id="SCAN" name="SCAN"></span>

### SCAN and VIP

The SCAN and VIP functions of YAC can be used in combination, and can be specified for installation through the following options in the corresponding command of [Step 1: Generate Configuration File] in the example installation process. 

- **--public-network**: Regardless of whether SCAN or VIP is deployed, this option must be specified to configure the public network segment.

- **--scanname**： When using the SCAN function, use this parameter to specify the SCAN domain name, which must be a domain name that has been configured in the DNS server.

- **--vips**: When using the VIP function, use this parameter to specify the list of reserved virtual IP addresses.

The following are the parameter items generated in the configuration file when the above options are specified simultaneously.

```toml
...
scan_port = "1688"          # SCAN listening port, defaulting to the port value of LISTEN_ADDR.
scanname = "scan.example.com"  # SCAN domain name 
...

[[group]]
  ...
  [group.config]        
    ...

  [[group.node]]
    ...
    vip = "192.168.1.62/24" # VIP address, with one corresponding to each node.
    ...
```

In primary-standby cluster deployment, since the network segments, SCAN domain names, and VIP information of primary-standby clusters are all different, the corresponding values generated for the standby cluster in the configuration file according to the above option values need to be manually adjusted to actual values.

Subsequent steps are identical. 

If you are not sure whether to use SCAN or VIP, you can install YashanDB without specifying the above options. YashanDB supports adding, modifying, and deleting [SCAN](../../../Database Administration/Cluster Management/SCAN Management) or [VIP](../../../Database Administration/Cluster Management/VIP Management) on an existing cluster.

<span id="installation" name="installation"></span>

## YAC Deployment


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```




Please obtain the storage device configuration information in advance for specifying the --disk-found-path, --system-data, and --data parameters.

```shell
# Query the bound disk paths
$ ll /dev/yfs
```



### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package ce gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.

    ::: tabs
    == Default Example Scenario 

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 2 \
--disk-found-path /dev/yfs  \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0
```

    == SCAN and VIP

Taking the scenario where both SCAN and VIP functions are enabled as an example.

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 2 \
--disk-found-path /dev/yfs  \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0 \
--public-network 192.168.1.0/24/ens192 \
--vips 192.168.1.62/24,192.168.1.63/24 \
--scanname scan.example.com
```

    == Multiple Network Segments

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 --node 2 \
--disk-found-path /dev/yfs  \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0  \
--inter-cidr 172.16.1.0/24
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

|Parameter Item                |Adjustment Description                          |
| ---------------------------------------- | ------------------------------------------------------------ |
| scan_port                                | SCAN listening port, this parameter exists when configuring SCAN.<br>The port number defaults to the port number of LISTEN_ADDR. Please ensure it is consistent with the actually open port number, and it can be modified to the actual value. |
| Parameters under [group.config]          | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [[group.diskgroup]]     | These parameters are all disk group configurations for data disks and can be manually adjusted. |
| Parameters under [group.node.config]     | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.systemdiskgroup] | These parameters are all system disk group configurations and can be manually adjusted.<br>The system disk configuration **cannot be adjusted after installation**. Please complete the configuration of AU size, redundancy level, number of FailureGroups, and disk grouping according to your requirements at this step. |
| Parameters under [group.ycsconfig]       | These parameters are all [YCS configuration parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration) and can be manually adjusted. |
| Parameters under [group.yfsconfig]       | These parameters are all [YFS configuration parameters](../../../Database Administration/Storage Management/YFS Management/YFS Parameter Configuration) and can be manually adjusted. |
| CLUSTER_INTERCONNECT                     | This parameter defines the communication address and port between cluster instances.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |
| INTER_URL                                | This parameter defines the communication address and port between YCS instances.<br>The port number defaults to the port number of LISTEN_ADDR + 100. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1788". |
| Parameters under [[host]]                | When --inter-cidr is not specified, this type of parameter will not be generated in the configuration file.<br>After specifying the private IP address for each node, this type of parameter can be manually added, or adjustments can be made to the already generated parameters. |

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
    /data/yashan/yasdb_data/db-1-1
    ```


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


    The following output is completely consistent in the above scenarios.

    ```shell
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | ce        | 1-1:1  | 22573 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/ce-1-1 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0002 | ce        | 1-2:2  | 16935 | open            | normal          | primary       | 192.168.1.3:1688  | -           | /data/yashan/yasdb_data/ce-1-2 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
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




## Primary-Standby Cluster Deployment


Please log in as the installation user (taking yashan as an example) to the server where the software package has been downloaded (this document uses 192.168.1.2 as an example), and enter the installation directory where the software package is located (taking /home/yashan/install as an example). 

```shell
$ cd /home/yashan/install
```



Please obtain the storage device configuration information in advance for specifying the --disk-found-path, --system-data, and --data parameters.

```shell
# Query the bound disk paths
$ ll /dev/yfs
```



### Step 1: Generate Configuration File



This step is used to generate two important configuration files for YashanDB products: hosts.toml and {cluster_name}.toml. The cluster name comes from the parameter value of the --cluster option. This document uses yashandb as an example, and this configuration file will be referred to as yashandb.toml in the following text.

hosts.toml and yashandb.toml define the deployment information and database creation information of YashanDB, and are also used for subsequent operations such as scaling and upgrading of the product. Therefore, please do not make manual adjustments once confirmed (i.e., after deployment is completed and put into operation).

Before proceeding to the next step, the command in this step can be run repeatedly, and each run will directly overwrite the previously generated hosts.toml and yashandb.toml files.

1. Execute the yasboot package ce gen command to generate the configuration file. For the complete set of parameters for this command, please refer to the [yasboot](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot package) chapter. 

    The command examples shown below include the mandatory input options for the command, as well as the options we recommend you to input. Please customize the option parameters according to the actual situation.

    The --ip option value should be entered in primary-standby order. Multiple IP addresses are entered one by one, separated by commas. Consecutive IP addresses can be entered using range format, for example, 192.168.1.[2-3].

    The --group option value should enter the number of YACs, the --node option value will be used as the number of nodes in the primary cluster, and the --standby-node option value will be used as the number of nodes in the standby cluster.
    
    The following command deploys two YACs with one primary and one standby. Please adjust the parameters according to the actual situation.

    :::tabs
    == Default Example Scenario

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.10.4,192.168.10.5 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--disk-found-path /dev/yfs \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0 \
--node 2 --group 2 --standby-node 2
```

    == SCAN and VIP

Taking the scenario where both SCAN and VIP functions are enabled as an example.

After the configuration file is generated, the relevant configuration item values on the standby cluster need to be manually adjusted.

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.10.4,192.168.10.5 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--disk-found-path /dev/yfs \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0 \
--node 2 --group 2 --standby-node 2 \
--public-network 192.168.1.0/24/ens192 \
--vips 192.168.1.62/24,192.168.1.63/24 \
--scanname scan.example.com
```

    == Multiple Network Segments

In production environments, primary-standby clusters are generally located in different network segments. The --inter-cidr option cannot be specified, but instead must be implemented by modifying the configuration file.

After the configuration file is generated, the relevant configuration item values on the standby cluster need to be manually adjusted.

```shell
$ ./bin/yasboot package ce gen --cluster yashandb \
-u yashan -p yashan_password --ip 192.168.1.2,192.168.1.3,192.168.10.4,192.168.10.5 --port 22 \
--install-path /data/yashan/yasdb_home \
--data-path /data/yashan/yasdb_data \
--log-path /data/yashan/log \
--begin-port 1688 \
--disk-found-path /dev/yfs \
--system-data /dev/yfs/sys0,/dev/yfs/sys1,/dev/yfs/sys2 \
--data /dev/yfs/data0 \
--node 2 --group 2 --standby-node 2 \
--inter-cidr 172.16.1.0/24
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
| public_network                             | Public network segment, this parameter exists when configuring SCAN and VIP.<br>This parameter item is not generated on the standby cluster and needs to be manually added. |
| scan_port                                  | SCAN listening port, this parameter exists when configuring SCAN.<br>The port number defaults to the port number of LISTEN_ADDR. Please ensure it is consistent with the actually open port number, and it can be modified to the actual value.<br>This parameter item is not generated on the standby cluster and needs to be manually added. |
| scanname                                   | SCAN domain name, this parameter exists when configuring SCAN.<br>This parameter item is not generated on the standby cluster and needs to be manually added. |
| Parameters under [group.config]            | These parameters are all critical [database creation parameters](../../../Tools Guide/yasboot/Database Creation Parameters). Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [[group.diskgroup]]       | These parameters are all disk group configurations for data disks and can be manually adjusted. |
| vip                                        | VIP address, this parameter exists when configuring VIP.<br>This parameter item is not generated on the standby cluster and needs to be manually added on each node. |
| Parameters under [group.node.config]       | These parameters are all [database configuration parameters](../../../Reference Manual/Configuration Parameters) for the instance deployed on this node. Parameters not listed in the file adopt default values. For production environments, please be sure to adjust according to actual conditions. |
| Parameters under [group.systemdiskgroup]   | These parameters are all system disk group configurations and can be manually adjusted.<br>System disk configuration cannot be adjusted after installation. |
| Parameters under [group.ycsconfig]         | These parameters are all [YCS configuration parameters](../../../Database Administration/Cluster Management/Cluster Parameter Configuration) and can be manually adjusted. |
| Parameters under [group.yfsconfig]         | These parameters are all [YFS configuration parameters](../../../Database Administration/Storage Management/YFS Management/YFS Parameter Configuration) and can be manually adjusted. |
| CLUSTER_INTERCONNECT                       | This parameter defines the communication address and port between cluster instances.<br>The port number defaults to the port number of LISTEN_ADDR + 1. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1689". |
| INTER_URL                                  | This parameter defines the communication address and port between YCS instances.<br>The port number defaults to the port number of LISTEN_ADDR + 100. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1788". |
| REPLICATION_ADDR                           | This parameter defines the address and port of the primary-standby replication link.<br>The port number defaults to the port number of LISTEN_ADDR + 2. Please ensure it is consistent with the actually opened port number, and can be modified to the actual value.<br>In multi-segment network scenarios, the address part of this parameter must be left empty, for example ":1690". |
| Parameters under [[host]]                  | When --inter-cidr is not specified, this type of parameter will not be generated in the configuration file.<br>After specifying the private IP address for each node, this type of parameter can be manually added, or adjustments can be made to the already generated parameters. |

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
    /data/yashan/yasdb_data/db-1-1
    ```


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


    The following output is completely consistent in the above scenarios.

    ```shell
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address    | source_node | data_path                      |
    +--------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | ce        | 1-1:1  | 22573 | open            | normal          | primary       | 192.168.1.2:1688  | -           | /data/yashan/yasdb_data/ce-1-1 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0002 | ce        | 1-2:2  | 16935 | open            | normal          | primary       | 192.168.1.3:1688  | -           | /data/yashan/yasdb_data/ce-1-2 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0001 | ce        | 2-1:3  | 19703 | open            | normal          | standby       | 192.168.10.4:1688 | 1-1:1       | /data/yashan/yasdb_data/ce-2-1 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
    | host0002 | ce        | 2-2:4  | 19651 | open            | normal          | standby       | 192.168.10.5:1688 | 1-1:1       | /data/yashan/yasdb_data/ce-2-2 |
    +----------+-----------+--------+-------+-----------------+-----------------+---------------+-------------------+-------------+--------------------------------+
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




### Step 7 (Optional): Enable *yasom* Election

In Primary-Standby YAC Deployment, if you need to enable cluster-level automatic leader election, please refer to the operational instructions described in the [Configuring yasom Election](../../../High Availability/Configuring Leader Election/Configuring yasom Election) document for configuration.
