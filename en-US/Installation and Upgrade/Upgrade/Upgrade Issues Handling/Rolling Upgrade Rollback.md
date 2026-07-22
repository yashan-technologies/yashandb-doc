During the database upgrade process, the upgrade may fail due to reasons such as network failures, command errors, configuration file errors, and version errors. In this case, please run the rollback to restore the environment before the upgrade.

> **Warn**:     
>
> - Rollback is only allowed if the upgrade fails.
> - The standby database will be restarted during the rollback process.
> - During the rollback process, other operations on the database nodes are not recommended. If unavoidable, only use *yasboot* to perform related management operations.
> - If the upgrade fails and the rollback also fails, please contact our technical support for assistance.

If the upgrade is successful and a rollback is executed, it will report an error:

```shell
$ ./bin/yasboot cluster rollback -c yashandb --rolling
yashandb can't rollback because upgrade result is success
```

## Rollback for Rolling Upgrade Between Compatible Versions

### Step 1: Roll back the database

Expected result when executing the rollback in case of upgrade failure:

```shell
$ ./bin/yasboot cluster rollback -c yashandb --rolling
+---------------------------------------------------------------------------------------------------------------------+
| type  | uuid             | name                 | hostid   | index        | status  | return_code | progress | cost |
+---------------------------------------------------------------------------------------------------------------------+
| task  | c82ebcbb5fb4acd3 | RollbackYasdbCluster | -        | yashandb     | SUCCESS | 0           | 100      | -    |
+-------+------------------+------------------------+--------+--------------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

Parameter introduction:

```shell
-c, --cluster Cluster name
--rolling Rolling upgrade rollback
```

If a rollback failure occurs and the primary database count is 0, please stop user operations via application and then use the following command to restart the old version database:
```shell
$ ./bin/yasboot cluster restart -c yashandb
+-------------------------------------------------------------------------------------------------------------+
| type | uuid             | name                | hostid | index    | status  | return_code | progress | cost |
+-------------------------------------------------------------------------------------------------------------+
| task | 93639d0b633d4eeb | ReStartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | -    |
+------+------------------+---------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

### Step 2: Roll back yasom and yasagent

```shell
$ ./bin/yasboot package rollback -c yashandb -t /home/yashan/install/hosts.toml
rollback package...
host0001 100% [====================================================================]    2s
```

Parameter introduction:

```shell
-c, --cluster Cluster name
-t, --toml  Configuration file for the server hosts.toml, which refers to the hosts.toml file generated during the deployment of the old version
```

## Rollback for Incompatible Versions Between Compatible Rolling Upgrades

For rolling upgrades between incompatible versions, there are the following two scenarios, along with corresponding actions.

- Scenario 1: The primary node is an old version, rolling back to the old version.
- Scenario 2: The primary node is a new version, only supporting continued upgrades.

Execute the following command to determine which scenario it is based on the output results.

```shell
$ ./bin/yasboot cluster rollback -c yashandb --rolling
```

Parameter introduction:

```shell
-c, --cluster Cluster name
--rolling Rolling upgrade rollback
```

### Scenario 1: The primary node is an old version

If the following task list appears, it indicates that the primary node is an old version.

```shell
+---------------------------------------------------------------------------------------------------------------------+
| type  | uuid             | name                 | hostid   | index        | status  | return_code | progress | cost |
+---------------------------------------------------------------------------------------------------------------------+
| task  | c82ebcbb5fb4acd3 | RollbackYasdbCluster | -        | yashandb     | SUCCESS | 0           | 100      | -    |
+-------+------------------+------------------------+--------+--------------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

After successfully rolling back the database, execute the following command to roll back yasom and yasagent.

```shell
$ ./bin/yasboot package rollback -c yashandb -t /home/yashan/install/hosts.toml 
rollback package...
host0001 100% [====================================================================]    2s
```

Parameter introduction:

```shell
-c, --cluster Cluster name
-t, --toml  Configuration file for the server hosts.toml, which refers to the hosts.toml file generated during the deployment of the old version
```

### Scenario 2: The primary node is a new version

If an error occurs indicating the primary node is a new version, it means the primary node is indeed a new version.

```shell
check that the version of primary node is newer, please execute 'yasboot cluster upgrade --rolling --continue' to continue upgrade
```

According to the prompt, add the --continue parameter to continue the upgrade.

```shell
./bin/yasboot cluster upgrade -c yashandb --rolling --continue
+------------------------------------------------------------------------------------------------------------------+
| type | uuid             | name                       | hostid | index    | status  | return_code | progress | cost |
+------------------------------------------------------------------------------------------------------------------+
| task | 5729a3e06b5fc3aa | UpgradeYasdbClusterRolling | -      | yashandb | SUCCESS | 0           | 100      | 27   |
+------+------------------+----------------------------+--------+----------+---------+-------------+----------+------+
task completed, status: SUCCESS
```

## Post-Rollback Operations

If certain configurations were temporarily adjusted for the smooth execution of the upgrade (e.g., disabling arbitration), after rollback, if there is no need for further upgrades, the configurations should be restored as needed (for example, re-enable [yasom election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)).
