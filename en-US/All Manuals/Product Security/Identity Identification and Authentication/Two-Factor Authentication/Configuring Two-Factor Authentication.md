## Enable/Disable Two-Factor Authentication Functionality

YashanDB does not enable two-factor authentication functionality by default. If you need to use this functionality, please contact our technical support for assistance.

## Enabling Two-Factor Authentication for Users

After enabling the two-factor authentication functionality, if you need to enable two-factor authentication for a specific database user, you must ensure that the user is one of the DBA, SECURITY_ADMIN, or AUDIT_ADMIN roles.

### Step 1: Initialize UKEY

This document uses the [yaspwd](../../../Tools Guide/yaspwd) tool as an example. You may choose an appropriate tool in actual operations.

1. Log in to the server where the database is installed using the database installation user.

2. Insert a UKEY into the USB port of the server, ensuring that there is only one UKEY on the server.

3. Select the corresponding *yaspwd* command to initialize the UKEY based on the user role.

    The user role can only be DBA, SECURITY_ADMIN, or AUDIT_ADMIN, and must not be chosen incorrectly.
    ```shell
    # User is DBA (Database Administrator)
    $ yaspwd ukey_role=DBA

    # User is SECURITY_ADMIN (Security Administrator)
    $ yaspwd ukey_role=SECURITY_ADMIN

    # User is AUDIT_ADMIN (Auditor)
    $ yaspwd ukey_role=AUDIT_ADMIN
    ```
    
4. Follow the prompts to enter the data device authentication code of the UKEY and the admin password, as well as the database user's login password, to complete the initialization and generate the public key file.

    ```shell
    # The following file and storage path are for example only, please refer to the actual one
    # User is DBA (Database Administrator), their public key file is:
    $YASDB_HOME/bin/dba.pub

    # User is SECURITY_ADMIN (Security Administrator), their public key file is:
    $YASDB_HOME/bin/security_admin.pub

    # User is AUDIT_ADMIN (Auditor), their public key file is:
    $YASDB_HOME/bin/audit_admin.pub
    ```

    > **Note**:
    >
    > If it is a multi-server database environment, you should manually copy the corresponding public key files to the same path on each server.

### Step 2: Configure Public Key

1. Log in to the database with a user that has ALTER SYSTEM privilege.

2. Execute the ALTER SYSTEM SET PARAMETER statement to update the path of the corresponding public key file.

    ```sql
    -- This document is for example only; the public key file path should use the standard path format
    -- If it is an ISC Distributed Cluster Deployment, TYPE = ALL must also be specified

    -- User is DBA (Database Administrator)
    ALTER SYSTEM SET UKEY_DBA_PUBLIC_KEY_FILE=$YASDB_HOME/bin/dba.pub;

    -- User is SECURITY_ADMIN (Security Administrator)
    ALTER SYSTEM SET UKEY_SECURITY_ADMIN_PUBLIC_KEY_FILE=$YASDB_HOME/bin/security_admin.pub;

    -- User is AUDIT_ADMIN (Auditor)
    ALTER SYSTEM SET UKEY_AUDIT_ADMIN_PUBLIC_KEY_FILE=$YASDB_HOME/bin/audit_admin.pub;
    ```

3. Restart the database to make the configuration take effect.