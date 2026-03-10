During the database upgrade process, the upgrade may fail due to reasons such as network failures, command errors, configuration file errors, and version errors. In this case, please run the rollback to restore the environment before the upgrade.

> **Warn**:
>
> - Rollback is only allowed if the upgrade fails.
> - The database will be restarted during the rollback process.
> - During the rollback, it is not recommended to perform other operations on the database nodes. If unavoidable, only use *yasboot* to perform relevant management operations.
> - If the upgrade fails and the rollback still fails, please contact our technical support for assistance.

Executing rollback after a successful upgrade will result in an error:

```shell
$ ./bin/yasboot cluster rollback -c yashandb
yashandb can't rollback because upgrade result is success
```

## Step 1: Rollback the Database

Expected result when executing the upgrade rollback after a failure:

```shell
$ ./bin/yasboot cluster rollback -c yashandb
+---------------------------------------------------------------------------------------------------------------------+
| type  | uuid             | name                 | hostid   | index        | status  | return_code | progress | cost |
+---------------------------------------------------------------------------------------------------------------------+
| task  | c82ebcbb5fb4acd3 | RollbackYasdbCluster | -        | yashandb     | SUCCESS | 0           | 100      | -    |
+-------+------------------+----------------------+----------+--------------+---------+-------------+----------+------+

task completed, status: SUCCESS
```

Parameter introduction:

```shell
-c, --cluster Cluster name
```

## Step 2: Rollback yasom and yasagent

>**Note**:
>
> This step is required only if the upgrade fails between versions 23.2.x and above.

```shell
$ ./bin/yasboot package rollback -c yashandb -t /home/yashan/install/hosts.toml 
rollback package...
host0001 100% [====================================================================]    2s
```

Parameter introduction:

```shell
-c, --cluster Cluster name
-t, --toml  Required server configuration file hosts.toml, referring to the hosts.toml file generated during the deployment of the old version
```

## Step 3: Post-Rollback Operations

If certain configurations were temporarily adjusted for a successful upgrade operation (e.g., disabling arbitration), after the rollback, if no further upgrades are planned, configurations should be restored as needed (e.g., re-enabling [yasom election](../../../高可用/自动选主配置/一主一备yasom仲裁选主)).