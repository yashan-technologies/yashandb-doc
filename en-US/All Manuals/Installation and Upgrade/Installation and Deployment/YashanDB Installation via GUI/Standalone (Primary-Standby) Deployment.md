## Step 1: Start the Web Service
1. Log in to the server 192.168.1.2 as the user yashan.

2. Execute the following command to navigate to the directory where the Web service is installed.
    ```shell
    $ cd /home/yashan/install/om
    ```

3. Execute the following command to start the Web server using yasom:
    ```shell
    $ ./bin/yasom --web --listen 192.168.1.2:9001
    ```
    - --web: Specifies to start as a Web server.

    - --listen: Specifies the listening address (the visual installation web address), formatted as `IP:PORT`, usually set to the current server's IP, and port 9001 is recommended.

4. Access the visual installation web address in the PC's browser.

## Step 2: Configure Database Basic Information and Server Information

1. Configure basic database information according to actual conditions:

   - DB name: Enter the database cluster name; this name will also serve as the initial database name (database name). It must start with a letter and be between [1,63] characters long, such as yashandb.

   - DB deployment type: Choose the database deployment type, such as Standalone Deployment.

> **Note**:
>
> If you need to reuse/clear the configuration record in the current environment (possible scenarios for retaining configuration information: uninstallation after successful visual installation of the database, failure of visual installation, etc.), you can click on the [DB Name] input box and select/clear the corresponding configuration from the dropdown options.
> 
> ![clear_cluster_config](./image/clear_cluster_config.png)

2. In the server list, the information of the server where the Web service is located will be recognized by default. After confirming that the installation path and other information are correct, click [Try to verify] to check correctness.

3. (Optional) If you need to deploy a primary/standby high-availability environment, click [Add] in the upper right corner of the server list to add other server information, then click [Confirm] to save the configuration, and click [Verify All] to check correctness.
   
   - Server address: The server's IP address, format: `192.168.1.3` or `192.168.1.[3-4]`, allowing multiple IP addresses/sets configured, separated by line breaks.
   
   - User group: The user group to which the installation user belongs; if not filled, it defaults to the same as the username.
   
   - Username: The name of the installation user, for example, yashan.

   - Password: Optional parameter, the password of the installation user. If the current server is configured for SSH keyless access to other servers, there is no need to fill in the password.

   - SSH port: SSH port, for example, 22.

   - Installation path: Database installation path, recommended to configure to the planned [HOME directory](../安装前准备/创建用户和目录.html#HOMEDirectory)/{version_number} (there is no check for the existence of the/{version_number} subdirectory; it will be created automatically during installation), supports numbers, letters (case-sensitive), and some symbols (`/`, `-`, `_`, `.`), for example, /data/yashan/yasdb_home/{version_number}.

   - Log path: [Running Log Directory](../安装前准备/创建用户和目录.html#run_log_path), for example, /data/yashan/log.

4. After confirming that the information is correct, click [Next step].

## Step 3: Configure Server Sudo

1. In the database configuration area, you can configure the following functionalities:

   - Whether to create a cgroup: Enables the creation of a cgroup directory for YashanDB's CPU resource management and requires filling in the cgroup directory for other server configurations. This parameter only needs to be configured when installing a standalone database that can enable CPU resource management (not for cascade backup).

   - Whether to start monit at Boot: When enabled, the daemon will automatically start after the server boots up and pull up various YashanDB processes, indirectly achieving the automatic startup of the database.

   - Add the user to the YASDBA User Group: When enabled, it adds the installation user to the YASDBA group, allowing passwordless login to the database.

   The above functionalities require the installation user to have sudo privileges; this example uses default settings, only enabling adding the user to the YASDBA user group.
   
   > **Note**:
   >
   > If the [Add the user to the YASDBA User Group] parameter is set to off but later related functionalities are needed, refer to [Configuring Boot Autostart](../Initial Environment after Installation/Configuring Boot Autostart) to complete the relevant configuration.

2. After confirming that the information is correct, click [Next step].

## Step 4: Configure Cluster Node Information

1. If you need to adjust node-related configurations, click [Edit] in the [Node scale] area. Adjust the relevant configurations based on actual conditions and click [Confirm] to save the information.

   - Grammatical mode: Choose between yashan mode or mysql mode based on business needs. If selected as yashan mode, it cannot be directly switched to mysql mode after installation; reinstallation is required.
   
   - Number of node groups: The number of standalone node groups, defaulting to 1. If performing a dual-replication primary/standby deployment, fill in 2.

   - Number of standalone nodes: Select the number of database instances primary/standby on the server. If a dual-replication primary/standby deployment is performed, this indicates the number of nodes in the primary replication group. Default is 1; recommended to be 1 in production environments.

   - Number of cascade standby nodes: Select the number of cascade backup nodes on the server. Default is empty. It needs to be filled when deploying cascade backup.

   - Subscript of the standby node to which the cascade standby is bound.: After filling in the number of cascade backup nodes, you need to specify the index of the backup node bound to the cascade backup node.

   - Number of Backup Node Groups: The number of nodes in the backup replication group when performing dual-replication primary/standby deployment.

   - Begin port: Fill in the starting value of the database listening port. If there are multiple listening ports, the system will calculate it according to the [Port Partitioning Rules](../安装前准备/网络准备.html#openports); the default value is 1688.

   - Default node path: Specify the YashanDB data directory. If left empty, it defaults to the yasdb_data directory in the parent directory of the server installation path. **Changes after installation do not take effect**; supports numbers, letters (case-sensitive), and some symbols (`/`, `-`, `_`, `.`), up to 75 characters, for example, /data/yashan/yasdb_data.

   - Node run log path: Specify the run log path for YashanDB. If left empty, it defaults to the log directory in the parent directory of the server installation path; it is recommended to be the same as the log path in the host list. For example, /data/yashan/log.

   - Whether to enable recommended parameters: When the recommended configuration is enabled, yasom will call the [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM) advanced package to generate recommended parameters, overriding parameters of the same name. It is defaulted to be enabled. When enabled, the following parameters also need to be configured:
 
      - YashanDB memory limit: Set the percentage of server memory available to YashanDB; yasom will calculate the specific memory limit based on this percentage.

      - YashanDB CPU limit: Set the percentage of server CPU available to YashanDB; yasom will calculate the specific CPU limit based on this percentage.

      - Table type: Choose the table type commonly used by the main business. Modify the database configuration parameters to achieve maximize performance when using this table type in the database. Default is HEAP.

      - Scene for parameter tuning: Parameter tuning scenario, default is NORMAL.

   - Network card configuration: You can configure the [DB listening Address] and [Primary-standby replication link address] to different segments, formatted as `192.168.1.0/24`.

2. In the [SYS user profile] area, set the password for the database super administrator SYS user. The configuration requirements are as follows:

    - Password length should be between 8 to 64 characters.
    
    - The password cannot contain the corresponding database username.
    
    - The password must contain numbers, letters, and special characters simultaneously.

    - Special characters related to Linux OS commands (such as `@`, `/`, `.`, `!`, `$`, `'`, etc.) need to be escaped.

3. In the [yasom Configuration] area, you can adjust the server where the main yasom is located and the listening port based on actual conditions.

   - Server where yasom is located: Defaults to the current server IP.

   - LISTEN_ADDR: The listening port of yasom, default is 1675.

4. In the [Recommended parameters] area, check the configuration information; this configuration derives from the corresponding configuration in the node scale.
   
   ```shell
   After enabling the recommended configuration, some parameters will have fixed values and cannot be modified. The parameters are as follows:
   +--------------------------------+-------------+---------+
   +--------------------------------+-------------+---------+
   |            name                |  recommend  | restart |
   +--------------------------------+-------------+---------+
   | DATA_BUFFER_SIZE               |       5498M |  True   |
   | VM_BUFFER_SIZE                 |        741M |  True   |
   | WORK_AREA_STACK_SIZE           |          1M |  True   |
   | WORK_AREA_POOL_SIZE            |         16M |  True   |
   | WORK_AREA_HEAP_SIZE            |       2048K |  True   |
   | SHARE_POOL_SIZE                |        741M |  True   |
   | LARGE_POOL_SIZE                |        112M |  True   |
   | MAX_PARALLEL_WORKERS           |          12 |  True   |
   | SCOL_DATA_BUFFER_SIZE          |        128M |  True   |
   | SCOL_DATA_PRELOADERS           |           2 |  True   |
   | COLUMNAR_WORK_AREA_HEAP_SIZE   |         32M |  True   |
   | COLUMNAR_VM_BUFFER_SIZE        |        128M |  True   |
   | COLUMNAR_BULK_SIZE             |        1024 |  True   |
   | COMPRESSION                    |         LZ4 |  True   |
   | PQ_POOL_SIZE                   |        128M |  True   |
   | MAX_SESSIONS                   |         128 |  True   |
   | MAX_WORKERS                    |           0 |  True   |
   | TAB_QUEUE_WINDOW_SIZE          |           8 |  True   |
   | BLOOM_FILTER_FACTOR            |         0.5 |  True   |
   | DEGREE_OF_PARALLEL             |           1 |  True   |
   | MMS_DATA_LOADERS               |           3 |  True   |
   | CHECKPOINT_INTERVAL            |        192M |  False  |
   | CHECKPOINT_TIMEOUT             |          60 |  False  |
   | REDOFILE_IO_MODE               |      DIRECT |  True   |
   | DATAFILE_IO_MODE               |     DEFAULT |  True   |
   | COMMIT_LOGGING                 |   IMMEDIATE |  False  |
   | RECOVERY_PARALLELISM           |           2 |  True   |
   | REDO_BUFFER_SIZE               |         16M |  True   |
   +--------------------------------+-------------+---------+
   ```

5. In the [Plugin Configuration] area, select the plugins you need to install as required.

6. In the [yasagent Configuration] area, you can adjust the following configurations as needed:

   - yasagent LISTEN_ADDR: The listening port of yasagent, default is 1676.

   - DB adaptive memory limit: When the recommended configuration is enabled, memory limit must be configured, formatted as `number + space/K/M/G/T`, with the value range [number_of_instances * 1536M, maximum_memory_of_server].

   - Containing nodes: Displays the database instance information corresponding to the deployment on each server. Instances marked with a star have the primary role, and others are backups. When multiple servers exist, instances can be dragged to adjust their distribution.
7. In the [Node configuration] area, expand the database instance list and click on the instance name to view instance information and adjust specific configurations as needed.
 
   - Modify node scale, add or delete nodes/node groups. For example, click [Add a New node Group] to add a node group; click the [+] next to a node group (as shown in figure dbg1) to add nodes to that node group.

   - Expand the database instance list, click the instance name (as shown in figure dbg1-1) can view instance information and adjust relevant configurations as needed.

8. After confirming that the information is correct, click [Next step].

## Step 5: Set Database Creation Parameters

On the [DB creation parameters] page, refer to [Database Creation Parameters](../../../工具手册/yasboot/建库参数) to add/delete/edit corresponding parameters as needed. After confirming the information is correct, click [Next step].

## Step 6: Set Configuration Parameters

On the [DB node parameters] page, you can add/delete/modify the parameters for each database instance as needed. After confirming the information is correct, click [Save and Go to the Next Step].

## Step 7: Deploy the Database

1. On the [DB global information] page, confirm that the information is correct, then click [Deploy].

2. When the message below appears, it indicates that the deployment is complete. You can manually close the webpage, and the server will automatically exit after a certain period.

> **Note**:
>
> After deployment is complete, yasom will generate the hosts.toml and yashandb.toml files in the `/home/yashan/install/conf/SE/yashandb` directory, where yashandb is the database name. This directory is the installation directory.

## Step 8: Configure Environment Variables

After successful deployment, the installation path configured in the previous steps (for example, /data/yashan/yasdb_home/{version_number}) will generate a subdirectory /conf, where the environment variable file related to YashanDB `{cluster_name}.bashrc` will be automatically generated and needs to be applied to the operating system.

Log in to each server with the installation user and execute the following commands to make the environment variables effective.

```shell
# Navigate to the directory where the environment variable file is located, for example, /data/yashan/yasdb_home/{version_number}/conf
$ cd /data/yashan/yasdb_home/{version_number}/conf

# Apply environment variables
$ cat yashandb.bashrc >> ~/.bashrc
$ source ~/.bashrc

# Verify if Environment Variables Are Effective (Please use actual paths from the echo output)
$ echo $YASDB_DATA
/data/yashan/yasdb_data/db-1-1
```

For detailed information about environment variables, please refer to [Initial Environment After Installation > Environment Variables](../安装后初始环境/环境变量).

## Step 9: Check Installation Results

If there are connection errors or SQL statement execution errors, please check the installation steps according to the error messages, or consult our technical support.

1. Use the [yasql](../../../Tools Guide/yasql/User Guide for yasql) tool to connect to the database and check the instance status.

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

2. (Optional) Create a database user and grant permissions. For more operations, please refer to [User Management](../../../Product Security/Identity Identification and Authentication/Managing Users).

   ```shell
   SQL> CREATE USER sales IDENTIFIED BY sales;
      
   SQL> GRANT CONNECT TO SALES;
   ```
