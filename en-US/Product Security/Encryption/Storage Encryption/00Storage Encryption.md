The storage encryption capabilities provided by YashanDB include:

- [Password Encryption](Password Encryption): Supports the encryption of the password file for the sys user and supports the encryption of passwords for custom users.

- [Transparent Data Encryption](Transparent Data Encryption): Supports TDE at the tablespace, table, and column levels.

- [Backup Set Encryption](Backup Set Encryption): Supports specifying encryption policies during backup to ensure the security of backup set data.

> **Note**:
>
> If you need to use the national secret algorithm and TDE related key management functionality, please refer to [Dependency Preparation](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to check and ensure that the required tools are installed on the server system.