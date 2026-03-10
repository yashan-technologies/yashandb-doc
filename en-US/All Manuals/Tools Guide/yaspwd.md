The *yaspwd* tool is used to maintain the passwords of system users (such as SYS) in the database and to export the public key file of UKEY. The security level of this type of user is high, please operate this tool with caution.

Prerequisites
----

This tool depends on libyas_infra.so and libcrypto.so, and additionally requires ukey_gm3000.so to export the UKEY public key. Please ensure the following environment variable is configured.

```shell
$ export LD_LIBRARY_PATH=${YASDB_HOME}/lib:${LD_LIBRARY_PATH}
```

Command Format
----

The command format for using this tool is as follows:

```shell
yaspwd file=<fname> password=<password>
input_file=<input-fname>
'sys={y | password}'
'sysbackup={y | password}'
'sysdg={y | password}'
'syskm={y | password}'
```

Where:

- There should be no spaces before and after '='; otherwise, the command will not execute.
- When executing the same command multiple times, the subsequent input password will overwrite the previous password.
- The password for the SYS user is mandatory, meaning that a password must be specified when executing this command; other system users (SYSBACKUP/SYSDG/SYSKM) are optional.

> **Note**: 
>
> Currently, the only system user in YashanDB is SYS, and only the SYS user password needs to be specified.

**fname**

The password file name, which can specify a path. If no path is specified, the file will be generated in the current directory. It cannot be specified as an existing file; otherwise, an error will be reported.

> **Caution**: 
>
> This tool supports specifying the password file to any location, and it can be successfully generated, but whether the password file is effective depends on the YashanDB configuration parameter PASSWORD_FILE, which is used to define the path and file name for obtaining system user passwords. The default initial value is $YASDB_DATA/instance/yasdb.pwd.
>
> Users can only make the password in the file effective by storing the password file according to the path and name specified by PASSWORD_FILE.

**password**

A string composed of user-input characters, which must comply with the [Password Strength](../Product Security/Identity Identification and Authentication/Password Authentication/Password Policy.html#password_complexity) requirements.

**input-fname**

The password file name, which must be an existing file used to generate a new password file from its content.

**y**

When this option is specified, the user will be prompted to enter a new password for the corresponding user.

Generate Password
----

After the database installation is completed, the following steps can be followed to generate a new password for the system user:

1. Log in to the database server as the database installation user, and navigate to the path specified by the PASSWORD_FILE configuration parameter (default is $YASDB_DATA/instance/).

2. Check whether the yasdb.pwd file already exists in the path. If it exists, rename it.

    ```shell
    $ mv yasdb.pwd yasdb1.pwd
    ```

3. Use the *yaspwd* command to generate a password, where the SYS user password is the default and required item.

    ```shell
    # Method One: Specify only the password file and enter the SYS user's password as prompted
    $ yaspwd file=yasdb.pwd
    Enter password for SYS:

    # Method Two: Specify both the password file and the SYS user password
    $ yaspwd file=yasdb.pwd password=yasdb_123

    # Method Three: Specify passwords for other system users
    $ yaspwd file=yasdb.pwd sysbackup=y
    $ yaspwd file=yasdb.pwd password=yasdb_123 sysbackup=yasdb_1234

    # Method Four: Generate a new password file from another password file, copying the SYS user password
    $ yaspwd file=yasdb.pwd input_file=yasdb_input.pwd

    # Method Five: Generate a new password file from another password file and re-specify the SYS password (other system user passwords cannot be specified)
    $ yaspwd file=yasdb.pwd input_file=yasdb_input.pwd sys=y
    Enter password for SYS:
    ```

4. Use the new password to successfully log in to YashanDB.

Change Password
----

During the operation of the database, the system user password can be modified anytime via *yaspwd*.

> **Warn**:
>
> Passwords are the most basic authentication for database users. Please ensure to execute password modification operations without affecting the system's operation.

This tool can only modify the system user password of a single node. To modify the system user password of all nodes in the database cluster, refer to the description in the [yasboot](yasboot/Introduction to yasboot Command/yasboot cluster) chapter.

Users can follow the steps below to specify a new password for the system user:

1. Log in to the database server as the database installation user.

2. Use the *yaspwd* command to change the password.

    ```shell
    # Method One: Specify only the password file and enter the SYS user's password as prompted
    $ yaspwd file=yasdb_new.pwd
    Enter password for SYS:

    # Method Two: Specify both the password file and the SYS user password. If the password contains special characters related to OS commands (e.g., @, /, ., !, $, etc.), they must be escaped
    $ yaspwd file=yasdb_new.pwd password=yasdb_123

    # Method Three: Specify passwords for other system users. If the password contains special characters related to OS commands (e.g., @, /, ., !, $, etc.), they must be escaped
    $ yaspwd file=yasdb_new.pwd sysbackup=y
    $ yaspwd file=yasdb_new.pwd password=yasdb_123 sysbackup=yasdb_1234

    # Method Four: Generate a new password file from another password file, copying the SYS user password
    $ yaspwd file=yasdb_new.pwd input_file=yasdb_input.pwd

    # Method Five: Generate a new password file from another password file and re-specify the SYS password (other system user passwords cannot be specified)
    $ yaspwd file=yasdb_new.pwd input_file=yasdb_input.pwd sys=y
    Enter password for SYS:
    ```

3. Use the new password to log in to YashanDB. If login fails, continue to the next steps.

4. Log in to YashanDB with the old password and check the value of the PASSWORD_FILE parameter.

    ```sql
    SHOW PARAMETER PASSWORD_FILE;
    NAME                VALUE                                                            
    ------------------- -------------------------------------------------
    PASSWORD_FILE       ?/instance/a.pwd    
    ```

5. Modify the PASSWORD_FILE parameter value to the new password file (restart is required for the changes to take effect), or rename the new password file to the PASSWORD_FILE parameter value.

    ```sql
    -- Example of modifying the PASSWORD_FILE parameter. Please change the parameter value to the actual new password file path and name.
    ALTER SYSTEM SET PASSWORD_FILE=?/instance/yasdb_new.pwd SCOPE=SPFILE;
    -- After the above command is successful, restart the database.
    ```

6. Use the new password to successfully log in to YashanDB.

Export UKEY Public Key
----

Before using UKEY for authentication login, it is necessary to initialize the UKEY and export the public key.

> **Caution**: 
>
> During the initialization and export process of the UKEY, only one UKEY can be present on the device.

Users can follow the steps below to export the UKEY public key:

1. Insert the UKEY into the device, ensuring that only one UKEY is present.

2. Use the *yaspwd* command to specify the user role, initialize the UKEY, and export the corresponding UKEY public key.
    
    The user role can only be DBA, SECURITY_ADMIN, or AUDIT_ADMIN, and cannot be chosen incorrectly.

    DBA Role:
    ```shell
    $ yaspwd ukey_role=DBA
    ```
    Security Admin Role:
    ```shell
    $ yaspwd ukey_role=SECURITY_ADMIN
    ```
    Auditor Role:
    ```shell
    $ yaspwd ukey_role=AUDIT_ADMIN
    ```

3. If the device is not initialized, it will require the input of the device authentication code, admin password, and user password for initialization. If the device is already initialized, the user password will be required for UKEY authentication.

4. If the device already has a key pair, the prompt allows for either regenerating a new key pair or not needing to regenerate a key pair.

5. The public key file will be exported to the current directory, and the export will be successful as indicated by the prompt.