This article describes the process of uninstalling the YashanDB server using the [yasboot](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot cluster) command. The related operations must be executed under the installation path of the corresponding server (the example in this article is the install directory for the user yashan, that is, /home/yashan/install).

## Step 1: Check and Disable *yasom* Election

If it is a standalone one-primary/one-standby deployment or a distributed high-availability deployment (with DN group nodes as one-primary/one-standby), it is necessary to perform the current operation to check and disable yasom election before proceeding with subsequent operations.

1. Execute the following command to check whether yasom election is enabled:

    ```shell
    $ yasboot election config show -c yashandb
    group 1
      Protection Mode: MAXIMUM PROTECTION
      Members:
        [1-1:1] - Primary database
          [1-2:2] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode # Enabled indicates that yasom election is enabled
    ```

2. If it is enabled, execute the following command to disable the yasom election:

    ```shell
    $ yasboot election enable off -c yashandb
    ```

## Step 2: Uninstall Database

```shell
$ yasboot cluster clean --cluster yashandb --purge
```

## Step 3: Uninstall yasom and yasagent

```shell
$ yasboot package uninstall --cluster yashandb
```

> **Note**:
>
> If CPU management functionality of [Resource Management](../../数据库管理/资源管理/00资源管理) has been enabled, the `-t` parameter must be used in this command to clean up the autostart configuration of CPU resource management functionality.

## Step 4: Clean Up Environment

- Clear Environment Variables: Remove any YashanDB related [environment variables](../安装部署/安装后初始环境/环境变量) from ~/.bashrc on each server.

- Clear Residual Configurations: If autostart has been configured, it needs to be manually [disabled](../安装部署/安装后初始环境/配置开机自启动.html#disable).

- Clean Up Configuration Files (or move to another path for archiving): It is recommended to archive or clean up the \<cluster_name>.toml and hosts.toml files in the installation path. Otherwise, when redeploying a YAC with the same name in the same path on the original server, the YAS-05528 error may occur.

- Clean up installation packages and their extracted files and folders: Remove all YashanDB-related installation packages and their extracted files and folders from the installation server.


- For YACs of version 23.4.x.x and above, additional cleaning up residual processes. Execute the following command on each server to clean up the *ycsrootagent* process.

  ```shell
  $ sudo pkill -9 ycsrootagent
  ```

