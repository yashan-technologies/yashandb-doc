YashanDB supports rolling upgrades of deployed databases through the *yasboot* tool, allowing the binary files of the database to be upgraded to the new version without stopping the database.

## Applicable Scenarios

The applicable deployment types, version upgrades, and other constraints for rolling upgrade operations are shown in the table below.

The upgrade path must meet the requirement that the version number of the old version is less than that of the new version and that the release date of the old version is earlier than that of the new version.

When the table contains virtual columns, identity columns, or auto-increment column (mysql mode), it does not support rolling upgrades. Please use offline upgrades instead.

>**Note**:
>
> In this document, versions with the same first three digits but different fourth digits are referred to as "compatible versions." The dbcr directory of the database is the same between compatible versions.

|Deployment Type |Upgrade Path |Constraints |
|--------------------|---------------------|-----------------|
| Standalone Primary-Standby Deployment (non-cascade standby)  | 23.2.1.0 and above → Compatible new versions | During the upgrade, the dbcr directory of both old and new databases will be checked for similarity. If they are not the same, rolling upgrade cannot proceed (the rolling upgrade operation will not be executed, and rollback is not involved). |
| Standalone Primary-Standby Deployment (non-cascade standby)  | 23.4.1.0 and above → All new versions | If the new and old versions are compatible versions (the dbcr directory will be verified during the upgrade), there are no additional constraints. Otherwise, the following constraints apply: <br/>* Upgrades depend on flashback, logical standby database, and other corresponding functionality constraints.<br/>* DDL operations, including object DDL and file DDL, are prohibited during the upgrade.<br/>* It is recommended to perform the upgrade during low business periods; excessive business volume may cause the logical standby database apply to lag behind the primary database, leading to upgrade failure. |
| YAC Deployment          | 23.4.2.0 and above → Compatible new versions<br />**Special Case**: 23.4.4.100 - 23.4.4.105 cannot be upgraded to 23.4.4.106 or later via rolling upgrade | * During the upgrade, the dbcr directory of both old and new databases will be checked for similarity. If they are not the same, rolling upgrade cannot proceed (the rolling upgrade operation will not be executed, and rollback is not involved). |
| ISC Distributed High Availability Deployment | 23.2.3.0 and above → 23.2.3.100 and above compatible new versions | * Both MN and DN must be in primary/standby deployment.<br/>* During the upgrade, the dbcr directory of both old and new databases will be checked for similarity. If they are not the same, rolling upgrade cannot proceed (the rolling upgrade operation will not be executed, and rollback is not involved). |

## Precautions

- Complete the required tasks in [Pre-Upgrade Preparation](../Pre-Upgrade Preparation) before proceeding with the upgrade.
- During the upgrade, it is not advisable to perform other operations on the database nodes. If unavoidable, use *yasboot* to execute relevant management operations.
- If the upgrade fails, please execute [Rolling Upgrade Rollback](../Upgrade Issues Handling/Rolling Upgrade Rollback) to restore the environment. If rollback still fails, please contact our technical support for resolution.
- After the upgrade, check the running logs for alarms regarding deprecated parameters; see the parameter initialization section examples in [Parameter Configuration](../../../Database Administration/Instance Management/Database Parameter Configuration). If deprecated parameters were used, refer to the corresponding parameter descriptions in [Configuration Parameters](../../../Reference Manual/Configuration Parameters) to determine whether the parameter is deprecated due to renaming and whether it needs to be changed to the corresponding new parameter. If the new parameter is not configured, the configuration value of the deprecated parameter will automatically convert to the new parameter value.

## Operation Steps

The upgrade process requires the use of the *yasboot* tool, and all steps must be performed on the server where the *yasboot* tool is located (which already has a hosts.toml file). For detailed commands related to *yasboot*, please refer to [yasboot](../../../Tools Guide/yasboot/00yasboot).

>**Note**:
>
> All paths, version numbers, users, passwords, and echo information in this document are for example only; please refer to the actual situation.

### Step 1: Upgrade yasom and yasagent

The following operations are executed in the directory where the upgrade package is stored.

1. Log in to the target server as the installation user.

2. Check the current versions of yasom and yasagent.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 102280      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/{old_version_number}/bin/yasagent --init -c yashandb -l 192.168.1.2:1676 --host-id host0001 -k                  -d
    yashan 102323      1  0 16:24 ?        00:00:00 /data/yashan/yasdb_home/{old_version_number}/bin/yasom --init -c yashandb -l 192.168.1.2:1675 -k                  -d
    # Get the bin directory of yasom and yasagent, execute -h command to obtain current version information
    $ /data/yashan/yasdb_home/{old_version_number}/bin/yasom -h
    Usages: yasom [<flags>] <command>

    yasom daemon process, version: Release {old_version_number}
    ```

3. Switch to the path of the new version package and execute the following command to upgrade yasom and yasagent.

    ```shell
    $ cd /home/yashan/tmp_upgrade
    $ ./bin/yasboot package upgrade -t /home/yashan/install/hosts.toml
    upgrade package...
    install version: yashandb {new_version_number}
    host0001 100% [====================================================================]    3s
    upgrade host to yasom...
    ```

   |Parameter |Description |
   |--------------------|--------------------------------------|
   | -t, --toml             | The path of the server configuration file, which is the original hosts.toml file in the database installation directory. |

4. Check the version of yasom and yasagent after the upgrade.

    ```shell
    $ ps -ef | grep -E "yasom|yasagent" | grep -v grep
    yashan 103607      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version_number}/bin/yasagent -c yashandb -d
    yashan 103650      1  0 16:32 ?        00:00:00 /data/yashan/yasdb_home/{new_version_number}/bin/yasom -c yashandb -d
    # The version has been upgraded to {new_version_number}
    ```

### Step 2: Upgrade the Database

> **Caution**:
>
> If yasom and yasagent have been upgraded but the database has not yet been upgraded, and the yasdb process terminates abnormally, you must first execute the `yasboot package rollback` command to revert yasom and yasagent before using the yasboot tool to perform database maintenance operations.
>
> If any errors occur during the database upgrade process, please execute [Rolling Upgrade Rollback](../Upgrade Issues Handling/Rolling Upgrade Rollback) to restore the environment. Once the upgrade is successful, rollback is not possible.

1. Check the current database version.

    ```shell
    $ ./bin/yasboot sql -d sys/********@192.168.1.2:1688 -s 'select version from v$instance;'
    
    VERSION                                                          
    ---------------------------------------------------------------- 
    Release {old_version_number} x86_64    
    # The current version is {old_version_number}                                    
    ```

2. Upgrade the database.

    ```shell
    $ ./bin/yasboot cluster upgrade --cluster yashandb --rolling
    +-------------------------------------------------------------------------------------------------------------+
    | type | uuid             | name                | hostid | index    | status  | return_code | progress | cost |
    +-------------------------------------------------------------------------------------------------------------+
    | task | 9d0041edd55f7d4f | UpgradeYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 23   |
    +------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

    > **Note**: 
    >
    > Using the `--keep-primary` parameter ensures that the primary database of the cluster remains the same as that of the previous primary database after the rolling upgrade.

3. Check the version of the database after the upgrade.

    ```shell
    $ ./bin/yasboot sql -d sys/********@192.168.1.2:1688 -s 'select version from v$instance;'
    
    VERSION                                                          
    ---------------------------------------------------------------- 
    Release {new_version_number} x86_64   
    # The version has been upgraded to {new_version_number}                                     
    ```

### Step 3: Post-Upgrade Operations

- Check Environment Variables

  The upgrade operation does not modify the server's `YASDB_HOME`, `YASDB_DATA`, `LD_LIBRARY_PATH`, or `PATH` environment variables.
  
  If `YASDB_HOME` was manually modified (for example, the symbolic link was removed and pointed directly to the target path), you need to manually update it again to correspond to the new version of the database after the upgrade. **If not updated, commands using tools like *yasql* will still point to the old version database.**

- Enable or Disable Certain Configurations

  If any configurations were temporarily adjusted for a successful upgrade (such as disabling arbitration), they should be reverted as needed after the upgrade (for example, re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election for One Primary and One Standby)).
