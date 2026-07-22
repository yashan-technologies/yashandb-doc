Usually, when performing uninstallation and reinstallation in the original environment, the database installation environment meets the product requirements, and there are no changes to the initial environment, related configurations, or database version, meaning there is no need to repeat the preparation steps before installation.

## Step 1: Uninstall YashanDB Server

Before uninstalling YashanDB, it is recommended to perform [data backup](../../Database Administration/Backup and Recovery/00Backup and Recovery) to ensure data integrity and security, so that a complete data copy is available for restoration or migration if needed.

1. For Standalone Deployment one-primary/one-standby deployment or distributed high-availability deployment (DN group nodes are one-primary/one-standby), please check and turn off yasom election first:

    ```shell
    $ yasboot election config show -c yashandb
    group 1
    Protection Mode: MAXIMUM PROTECTION
    Members:
        [1-1:1] - Primary database
        [1-2:2] - Physical standby database

    ……

    Automatic Failover: Enabled in Zero Data Loss Mode 

    # Enabled indicates that yasom election is enabled; disable it before proceeding to the next operation
    $ yasboot election enable off -c yashandb
    ```

2. Uninstall the database.

    ```shell
    $ yasboot cluster clean --cluster yashandb --purge
    ```

3. Uninstall yasom and yasagent.

    ```shell
    $ yasboot package uninstall --cluster yashandb
    ```

    > **Note**:
    >
    > If you have enabled [Resource Management](../../Database Administration/Resource Management/00Resource Management) CPU management functionality, use the `-t` parameter in this command to clean the auto-start configuration of CPU resource management functionality.

## Step 2: Clean Environment

- Clean up installation packages and their extracted files and folders: Remove all YashanDB-related installation packages and their extracted files and folders from the installation server.


- When using YAC Deployment, you must clean (or archive) the <cluster_name>.toml and hosts.toml files in the installation path; otherwise, a YAS-05528 error may occur during reinstallation.

- For YACs of version 23.4.x.x and above, additional cleaning up residual processes. Execute the following command on each server to clean up the *ycsrootagent* process.

  ```shell
  $ sudo pkill -9 ycsrootagent
  ```


## Step 3: Reinstall YashanDB Server

1. If you need to adjust the installation environment, refer to [pre-installation preparation](../Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation) to adjust the relevant configurations as needed.

2. Choose an appropriate method to [install YashanDB server](../Installation and Deployment/00Installation and Deployment) again.
