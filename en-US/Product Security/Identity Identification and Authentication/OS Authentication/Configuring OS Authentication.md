For specific configuration steps, please refer to [OS Authentication Configuration](../../../Database Administration/Basic Database Management/OS Authentication Configuration).

## Enable OS Authentication Functionality

This functionality switch is controlled by the value of the ENABLE_LOCAL_OSAUTH parameter in the database configuration file yasdb_net.ini. After YashanDB installation, ENABLE_LOCAL_OSAUTH is set to on by default, which means that the OS authentication functionality is enabled.

The yasdb_net.ini configuration file will not be generated automatically. If modifications are needed, you must manually create the file in the $YASDB_DATA/config directory before making changes.

## Grant OS Authentication to Users

When you need to enable OS authentication for a database administrator, please consider the following aspects before granting the authentication permission:

- Whether the administrator should have all database management privileges; if so, allow them to enable OS authentication.

- Whether the administrator has an OS account on the database server; if not, a request must be made to the OS administrator for creation.

To enable OS authentication for a user, the following prerequisites must be met:

- The OS authentication functionality must be enabled.

- A request must be made to the OS administrator to add the target OS user to the **YASDBA user group** (usually created during the [preparation work](../../../安装和升级/安装部署/安装前准备/创建安装用户) of YashanDB installation) and the **same name user group of the database installation user** (for example, yashan).

- If configuring OS authentication for non-install users, ensure that the privilege value of the DATA directory is not lower than 750 (default is 750).

## Cancel User's OS Authentication

After submitting a request to the OS administrator to remove a user from the YASDBA user group, that user will no longer be able to log in to the database using OS authentication.

When a user is no longer permitted to use OS authentication, the cancellation should be done promptly.

## Disable OS Authentication Functionality

>**Caution**:
>
> Functionalities such as [yasom election](../../../高可用/自动选主配置/一主一备yasom仲裁选主), [yasboot patrol](../../../工具手册/yasboot/yasboot命令介绍/yasboot patrol), etc., rely on OS authentication. **Disabling OS authentication functionality will render these functionalities unavailable**, so please proceed with caution.

To disable the method of using OS identity for authentication, set the value of the ENABLE_LOCAL_OSAUTH parameter in the database configuration file yasdb_net.ini to off. All database login requests must then use password authentication.
