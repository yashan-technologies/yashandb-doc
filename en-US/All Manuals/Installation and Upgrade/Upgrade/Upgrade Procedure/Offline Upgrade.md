YashanDB supports offline upgrades of deployed databases through the *yasboot* tool, upgrading the database binary files to a new version.

## Applicable Scenarios

The offline upgrade operation is suitable for all deployment forms. The upgrade path must satisfy that the version number of the old version is less than that of the new version and that the release date of the old version is earlier than that of the new version.

## Precautions

- The offline upgrade process requires a certain amount of time (especially in scenarios with primary/standby deployments and a large number of slices and data volumes. For example, upgrading from v23.2.8.100 and earlier versions to new versions may increase the estimated time by 10 minutes for every additional 100,000 slices). Please allow ample time for the operation.
- Please complete all tasks required for [pre-upgrade preparation](../Pre-Upgrade Preparation) before proceeding with the upgrade.
- During the upgrade process, it is not recommended to perform other operations on the database nodes. If unavoidable, only use *yasboot* to perform related management operations.
- If the upgrade fails, please follow the steps for [offline upgrade rollback](../Upgrade Issues Handling/Offline Upgrade Rollback). If the rollback also fails, please contact our technical support for assistance.
- After a successful upgrade, you need to determine whether any deprecated parameters are in use based on the running log warnings. Please refer to the parameter initialization section example in [Parameter Configuration](../../../Database Administration/Basic Database Management/Parameter Configuration). If deprecated parameters are in use, check the introduction of the corresponding parameters in [Configuration Parameters](../../../Reference Manual/Configuration Parameters) to determine if the parameter has become deprecated due to renaming and whether it is necessary to adjust the deprecated parameter to the corresponding new parameter. If the new parameter is not configured, the value of the deprecated parameter will be automatically converted to the new parameter value.

## Steps

The upgrade process requires the use of the *yasboot* tool, and all steps must be performed on the server where the *yasboot* tool is located (which has the hosts.toml file). For detailed introductions to *yasboot* commands, please refer to [yasboot](../../../Tools Guide/yasboot/00yasboot).

>**Note**:
>
> All paths, version numbers, users, passwords, and echo information in this document are for example only and should be subject to actual conditions.

### Step 1: Upgrade yasom and yasagent

The following operations are performed in the directory where the upgrade package is stored, and please choose the corresponding operation steps based on the actual upgrade scenario:

-  [Upgrade from 22.2.x.x to 23.2.x.x and above](#22)

- [Upgrade between 23.2.x.x and above](#23)

<span id="22" name="22" class="yaslink"></span>

#### Scenario 1: Upgrade from 22.2.x.x to 23.2.x.x and above

In the scenario of upgrading from 22.2.x.x to 23.2.x.x and above, upgrading yasom and yasagent is equivalent to using a higher version of *yasboot* to manage the old version of the database. You need to collect and organize various configuration information of the current database environment, including server information (IP, port, SSH username and password, etc.), the path information of the database ($YASDB_HOME, $YASDB_DATA, etc.), and the scale of primary/standby, etc.

1. Log in to the target server as the installation user.

2. Check whether yasom and yasagent processes exist in the current environment and their versions.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 102280      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasagent --init -c yashandb -l 192.168.1.2:1676 --host-id host0001 -k                  -d
    yashan 102323      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasom --init -c yashandb -l 192.168.1.2:1675 -k                  -d
    
    # Get the bin directory of yasom and yasagent, and execute the -h command to obtain current version information
    $ /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasom -h
    Usages: yasom [<flags>] <command>

    yasom daemon process, version: Release {old_version}
    ```

    If yasom and yasagent processes exist, execute the following command or use the OS's process termination command to stop them.

    ```shell
    $ /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasboot process yasom stop -c yashandb -t /home/yashan/install/hosts.toml && /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasboot process yasagent stop -c yashandb -t /home/yashan/install/hosts.toml
    stop yasom successfully
    stop agent host0002 successfully
    stop agent host0001 successfully
    ```

    |Parameter |Description |
    |--------------------|--------------------------------------|
    |  -c, --cluster     | Must specify the actual cluster name of current data |
    | -t, --toml         | Must specify the current database's server configuration file |

3. Change to the directory where the new version package is located.

   ```shell
   $ cd /home/yashan/tmp_upgrade
   ```

4. Based on the current database environment information, execute the following command to generate the server configuration file (hosts.toml).

   ```shell
   $ ./bin/yasboot package se gen --cluster yashandb \
   -u yashan -p password --ip ip1,ip2,……,ipn \
   --install-path /data/yashan/yasdb_home \
   --node 3
   -f
   ```
  
   |Parameter |Description |
   |--------------------|--------------------------------------|
   | --cluster          | The cluster name of the current database |
   |-u, --username      | The SSH username of the server         |
   |-p, --password      | The SSH user login password            |
   |--port               | Specify the SSH server port            |
   |--ip                | The IP addresses of the current database servers, separated by commas |
   | --install-path     | The installation directory of the current database |
   | --node             | The number of primary and standby databases in the current environment (not including the number of cascade standby). For example, if the database has 1 primary, 2 standby, and 1 cascade standby, this parameter should be set to 3 |

   > **Note**: 
   >
   > This command will generate the server configuration file (hosts.toml) and the database cluster configuration file (*cluster_name*.toml). Subsequent operations only need to use hosts.toml.

5. Execute the following command to initialize yasom and yasagent.

   ```shell
   $ ./bin/yasboot package install -t hosts.toml -f
   checking install package...
   install version: yashandb {version}
   host0001 100% [====================================================================]    3s
   update host to yasom...
   ```

   |Parameter |Description |
   |--------------------|--------------------------------------|
   | -t, --toml         | The server configuration file, which is the hosts.toml generated in the previous step |

6. Execute the following command to generate the management configuration template file join_demo.toml.

   ```shell
   $ ./bin/yasboot package config join-demo -t SE
   ```

   |Parameter |Description |
   |--------------------|--------------------------------------|
   | -t, --type         | The deployment form of the current database |

7. Modify the management configuration file join_demo.toml as needed and save it.

   ```shell
   $ vi join_demo.toml
   ```

   The content here is just an example. Please modify the parameter values in the file according to the actual situation:

   ```toml
   cluster = "yashandb"  	# Change to the cluster name, which can be custom. When subsequently using *yasboot* for database operations, this name should be used.
   sys_password = "yasdb_123"  # Change to the password of the target database sys user.
   yas_type = "SE"  			# Deployment form, typically does not need to be manually changed.
   
   [primary_config]
     manage_ip = "192.168.1.2"  								# Change to the IP of the server where the primary database is located.
     yasdb_home = "/opt/yasom/yashandb/yashandb"					# Change to the $YASDB_HOME directory of the primary database.
     node_path = "/opt/yasom/yashandb/data/yashandb/db-1-1"		# Change to the $YASDB_DATA directory of the primary database.
     node_id = "1-1:1"										# Node ID of the primary database, usually does not need to be manually changed.
   
   [[standby_config]]										# Modify according to the example of the primary database for standby database. If none, it can be deleted.
     manage_ip = "192.168.1.3"
     yasdb_home = "/opt/yasom/yashandb/yashandb"
     node_path = "/opt/yasom/yashandb/data/yashandb/db-1-2"
     node_id = "1-2:1"
   
   [[standby_config]]                                    # Modify according to the example of the primary database for standby database. If none, it can be deleted.
     manage_ip = "192.168.1.4"
     yasdb_home = "/opt/yasom/yashandb/yashandb"
     node_path = "/opt/yasom/yashandb/data/yashandb/db-1-3"
     node_id = "1-3:1"
   ```

8. Execute the following command to perform the management operation.

   ```shell
   $ ./bin/yasboot cluster join -t SE --config join_demo.toml
   ```

   |Parameter |Description |
   |--------------------|--------------------------------------|
   | -t, --type         | The deployment form of the current database |
   | -c, --config       | The management configuration file, which is join_demo.toml generated in the previous step |

   The command execution result is as follows:

   > **Caution**:
   >
   > If the status fails to display correctly, it is not recommended to continue management. Please check if the management configuration file is correct.

   ```shell
   the cluster status is as follow:
   |key         |value
   |------------+------
   |clusterName |yashandb
   |version     |{version}
   
   the cluster status is as follow:
   +----------------------------------------------------------------------------------------------------------------------------------------------------+
   | hostid   | node_type | nodeid | pid   | instance_status | database_status | database_role | listen_address     | data_path                         |
   +----------------------------------------------------------------------------------------------------------------------------------------------------+
   | host0001 | db        | 1-1:1  | 46689 | open            | normal          | primary       | 192.168.1.2:1688   | /data/yashan/yasdb_data/db-1-1    |
   +----------+-----------+--------+-------+-----------------+-----------------+---------------+--------------------+-----------------------------------+
   Check completed
   Are you sure you to add yasdb yashandb to yasom[yes/no]: yes
   +-------------------------------------------------------------------------------------------------------+
   | type | uuid             | name             | hostid | index | status  | return_code | progress | cost |
   +-------------------------------------------------------------------------------------------------------+
   | task | e2007922a317a02c | JoinYasdbCluster | -      | yashandb    | SUCCESS | 0           | 100      | -    |
   +------+------------------+------------------+--------+-------+---------+-------------+----------+------+
   task completed, status: SUCCESS
   ```

9. Check the version of yasom and yasagent again.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 103607      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version}/bin/yasagent -c yashandb -d
    yashan 103650      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version}/bin/yasom -c yashandb -d
    # The version is now {new_version}
    ```

<span id="23" name="23" class="yaslink"></span>

#### Scenario 2: Upgrade between 23.2.x.x and above

1. Log in to the target server as the installation user.

2. Check the current versions of yasom and yasagent.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 102280      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasagent --init -c yashandb -l 192.168.1.2:1676 --host-id host0001 -k                  -d
    yashan 102323      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasom --init -c yashandb -l 192.168.1.2:1675 -k                  -d
    # Get the bin directory of yasom and yasagent, and execute the -h command to obtain current version information
    $ /data/yashan/yasdb_home/yashandb/{old_version}/bin/yasom -h
    Usages: yasom [<flags>] <command>

    yasom daemon process, version: Release {old_version}
    ```

3. Switch to the directory where the new version package is located and execute the following command to upgrade yasom and yasagent.

    ```shell
    $ cd /home/yashan/tmp_upgrade
    $ ./bin/yasboot package upgrade -t /home/yashan/install/hosts.toml
    upgrade package...
    install version: yashandb {new_version}
    host0001 100% [====================================================================]    3s
    upgrade host to yasom...
    ```
    
   |Parameter |Description |
   |--------------------|--------------------------------------|
   | -t, --toml         | The path to the server configuration file, which is the existing hosts.toml file in the database installation directory |

4. Check the versions of yasom and yasagent after the upgrade.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 103607      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version}/bin/yasagent -c yashandb -d
    yashan 103650      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version}/bin/yasom -c yashandb -d
    # The version has been upgraded to {new_version}
    ```

### Step 2: Upgrade the Database

> **Caution**:
>
> If yasom and yasagent have been upgraded but the database has not yet been upgraded, and the yasdb process terminates abnormally, you must first execute the `yasboot package rollback` command to revert yasom and yasagent before using the yasboot tool to perform database maintenance operations.
>
> If any errors occur during the database upgrade process, please execute [offline upgrade rollback](../Upgrade Issues Handling/Offline Upgrade Rollback) to restore the environment, as a successful upgrade cannot be rolled back.

1. Check the current database version.

    ```shell
    $ ./bin/yasboot sql -d sys/********@192.168.1.2:1688 -s 'select version from v$instance;'
    
    VERSION                                                          
    ---------------------------------------------------------------- 
    Release {old_version} x86_64        
    # The current version is {old_version}                                 
    ```

2. Execute the corresponding command based on the database deployment form to upgrade the database.

    - Standalone Deployment, YAC Deployment (upgrading between versions 23.4.x.x and above), ISC Distributed Cluster Deployment 
        ```shell
        $ ./bin/yasboot cluster upgrade --cluster yashandb
        +---------------------------------------------------------------------- ---------------------------------------+
        | type | uuid             | name                | hostid | index    |   status  | return_code | progress | cost |
        +---------------------------------------------------------------------- ---------------------------------------+
        | task | 9d0041edd55f7d4f | UpgradeYasdbCluster | -      | yashandb |   SUCCESS | 0           | 100      | 23   |
        +------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
        task completed, status: SUCCESS
        ```

    - YAC Deployment (from 23.2.x.x and earlier to 23.4.x.x and above)

        ```shell
        $ ./bin/yasboot cluster upgrade --cluster yashandb --disk-config disk_config.toml
        +-------------------------------------------------------------------------------------------------------------+
        | type | uuid             | name                | hostid | index    |   status  | return_code | progress | cost |
        +-------------------------------------------------------------------------------------------------------------+
        | task | 9d0041edd55f7d4f | UpgradeYasdbCluster | -      | yashandb |   SUCCESS | 0           | 100      | 23   |
        +------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
        task completed, status: SUCCESS
        ```

        |Parameter |Description |
        |--------------------|--------------------------------------|
        | --disk-config       | Specify [YAC upgrade configuration file](../Pre-Upgrade Preparation.html#sys-data) |

    > **Note**: 
    >
    > During the upgrade process, the old version will automatically be backed up, and the backup files will be stored in the upgrade_tmp/backup directory under each server's installation directory.

3. Check the version of the database after the upgrade.

    ```shell
    $ ./bin/yasboot sql -d sys/********@192.168.1.2:1688 -s 'select version from v$instance;'
    
    VERSION                                                          
    ---------------------------------------------------------------- 
    Release {new_version} x86_64   
    # The version has been upgraded to {new_version}                                     
    ```
   
4. If this is a YAC upgrade scenario, the cluster needs to be restarted after a successful upgrade.

    ```shell 
    $ ./bin/yasboot cluster restart -c yashandb
    ```

### Step 3: Post-upgrade Operations

- Check Environment Variables

  The upgrade operation will not modify the server's `YASDB_HOME`, `YASDB_DATA`, `LD_LIBRARY_PATH`, and `PATH` environment variables.
  
  If you have manually modified `YASDB_HOME` (for example, removing the symbolic link to point directly to the target path), you need to manually update the environmental variables corresponding to the new version of the database after the upgrade. **If not updated, commands such as *yasql* will still point to the old version of the database.**

- Start the Daemon Process

  If the monit functionality needs to be enabled after the upgrade, execute the following command:

  ```shell 
  $ yasboot monit start --cluster yashandb
  ```

- Enable or Disable Certain Configurations

  If certain configurations were temporarily adjusted to smoothly execute the upgrade (such as disabling arbitration, disabling auto-start), they should be restored as needed after the upgrade (for example, re-enable arbitration, re-enable auto-start). 

  If the functionality supported by the new version is disabled by default after the upgrade (for example, YACs supporting VIPs), you can manually configure and enable the corresponding functionality according to the process (for example, [YAC configuration VIP](../../../YashanDB for Cluster/Yashan Cluster Service/VIP.html#vip_configuration)).

  >**Note**:
  >
  > Starting from version 23.2.1.x, the yasom election functionality depends on enabled [OS Authentication](../../../数据库管理/基本数据库管理/操作系统身份认证配置). If yasom election was enabled in versions below 23.2.1.x (which will be temporarily disabled during the pre-upgrade preparation), you must enable it again after upgrading to version 23.2.1.x or above. You should first check and enable the OS authentication to ensure the proper functioning of the election functionality.
