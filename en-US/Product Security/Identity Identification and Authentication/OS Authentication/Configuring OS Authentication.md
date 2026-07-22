This article mainly introduces how to configure OS authentication, allowing database administrators to log in to the database more conveniently.

## Enable OS Authentication Functionality

> **Note**:
>
> After following the [standard installation steps](../../../Installation and Upgrade/Installation and Deployment/00Installation and Deployment) to install YashanDB, the OS authentication functionality is enabled by default, and no additional operation is required. If the OS authentication functionality was previously disabled during usage, you can refer to this operation to re-enable it.

The state of this functionality is controlled by the value of the ENABLE_LOCAL_OSAUTH parameter in the database configuration file yasdb_net.ini. After installation of YashanDB, the default setting is ENABLE_LOCAL_OSAUTH = on, which means OS authentication functionality is enabled, and the yasdb_net.ini file will not be automatically generated.

1. Check if the yasdb_net.ini file exists in the $YASDB_DATA/config path.

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # This article takes /data/yashan/yasdb_data/db-1-1 as an example

    $ cd /data/yashan/yasdb_data/db-1-1/config 
    $ ll
    ```
    - If it does not exist, it indicates that it has been enabled, and no additional actions are needed.

    - If it exists, further actions are required.

2. Check the value of ENABLE_LOCAL_OSAUTH:

    ```shell
    $ vi yasdb_net.ini    
    ```
    If the value of ENABLE_LOCAL_OSAUTH is off, change it to on, save, and exit. Restart the database to apply the configuration.

## Enable OS Authentication for Users

When you need to enable OS authentication for a database administrator, please consider the following aspects before granting the authentication permission:

- Whether the administrator should have all database management privileges; if so, allow them to enable OS authentication.

- Whether the administrator has an OS account on the database server; if not, a request must be made to the OS administrator for creation.

### Preparation

- The OS authentication functionality must be enabled.

- A request must be made to the OS administrator to add the target OS user to the **YASDBA user group** (usually created during the [preparation work](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Creating Users and Directories) of YashanDB installation) and the **same name user group of the database installation user** (for example, yashan).

- If configuring OS authentication for non-install users, ensure that the privilege value of the DATA directory is not lower than 750 (default is 750).

### Operation Steps

1. Check if the YASDBA group exists. If not, create it (the `groupadd` command needs to be executed by root user or a user with sudo privilege):

    ```shell
    $ getent group YASDBA

    # Create YASDBA group if it does not exist
    $ groupadd YASDBA
    ```

2. Add the target OS account to the YASDBA group (the `usermod` command needs to be executed by root user or a user with sudo privilege):

    ```shell
    $ sudo usermod -aG YASDBA dba1
    $ groups dba1

    # If the target OS account is not the installation user, also add it to the same user group of the database installation user (e.g., yashan)
    $ sudo usermod -aG yashan dba1
    $ groups dba1
    ```

    > **Note**: 
    >
    > If the target user is currently logged in, the SSH connection needs to be disconnected and re-logged in to use the OS authentication functionality.

3. Verify OS authentication:

    ```shell
    $ yasql / as sysdba

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> SELECT SYS_CONTEXT ( 'USERENV' , 'SESSION_USER' ) FROM DUAL;

    SYS_CONTEXT('USERENV                                             
    ---------------------------------------------------------------- 
    SYS      
    ```

## Disable OS Authentication for Users

When a user is no longer permitted to use OS authentication, the cancellation should be done promptly.

- Method 1: Remove the target user from the YASDBA group. This user will no longer be allowed to use OS authentication, and it will take effect immediately.

- Method 2: Delete the YASDBA group. All users will no longer be allowed to use OS authentication, and it will take effect immediately. This operation is similar to disabling OS authentication. Unless absolutely necessary, **DO NOT perform this operation**.

## Disable OS Authentication Functionality

>**Caution**:
>
> Functions like [*yasom* election](../../../High Availability/Configuring Leader Election/Configuring yasom Election), and [*yasboot* patrol](../../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot patrol) depend on OS authentication. **Disabling OS authentication functionality will make these functions unavailable**, please proceed with caution.

To disable the method of using OS identity for authentication, set the value of the ENABLE_LOCAL_OSAUTH parameter in the database configuration file yasdb_net.ini to off. All database login requests must then use password authentication.

1. Check if the yasdb_net.ini file exists in the $YASDB_DATA/config path. If it does not exist, create it:

    ```shell
    $ echo $YASDB_DATA
    /data/yashan/yasdb_data/db-1-1 # This article takes /data/yashan/yasdb_data/db-1-1 as an example

    $ cd /data/yashan/yasdb_data/db-1-1/config
    $ vi yasdb_net.ini
    ```

2. Add or modify the following configuration in the yasdb_net.ini file:

    ```shell
    ENABLE_LOCAL_OSAUTH = off
    ```

3. Save and exit.

4. Restart the database to apply the configuration. The database will no longer perform OS authentication.