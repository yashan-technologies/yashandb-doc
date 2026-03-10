YashanDB provides key management and security assurance for [Transparent Data Encryption](Transparent Data Encryption) through a three-tiered key system and wallet mechanism, ensuring both the security of the keys and the efficiency of data encryption and decryption.

The key management functionality is not applicable to ISC Distributed Cluster Deployment.

## Three-Tier Key System

The three-tier key system refers to a hierarchical key management mechanism to ensure the confidentiality, integrity, and availability of data. This system manages and protects data with three different levels of keys, where each level of key plays different roles and provides layered protection for data privacy and integrity:

- Master Encryption Key (MEK): Also known as the first-level key, this is the highest level key used to encrypt/decrypt the global keys at the next level. It is manually created by the user and securely persisted in an external database wallet through encryption using the wallet mechanism.

- Global Encryption Key (GEK): Also known as the second-level key, this key is used to encrypt/decrypt data keys. It is automatically created by the database as needed and is persisted in the database control files after encryption with the master key.

- Data Encryption Key (DEK): Also known as the third-level key, this key is used to encrypt/decrypt the specific data stored in the database (such as tablespaces, tables, or column data). It is automatically created by the database as needed and is persisted in the database after encryption with the global key.

## Wallet

A wallet is a virtual container used for the independent external storage and management of database security credentials, enabling key sharing between servers in a database cluster. All data in the wallet is encrypted and requires a wallet password to unlock it, preventing malicious access to the wallet's contents.

> **Caution**:
>
> Please remember the wallet password and back up the wallet promptly. Loss of wallet-related data may render the database unavailable.

<span id="configuringwallet" name="configuringwallet" class="yaslink"></span>

## Configuring Wallet and Master Key

In Standalone/YAC/Distributed Cluster Deployment environments:

- Existing Encrypted Objects: For example, after upgrading from YashanDB 23.2 to YashanDB 23.4, the existing encrypted objects in the database can continue to be used without configuring a wallet and keys. If key management functionality is to be enabled for existing encrypted objects, then a wallet must be configured and used.

- Newly Added Encrypted Objects: A wallet must first be configured, and the wallet must be opened as required during the daily startup and shutdown of the database.

In ISC Distributed Cluster Deployment, there is no wallet and key management functionality; encrypted objects can be created and used directly.

>**Note**
>
> Configuring the wallet requires a database restart.

### Local Key Management



#### Preparation

- Plan and create a storage path for the wallet file on each database server. The YashanDB install user (i.e., yashan) must have full privileges for the corresponding path. This chapter takes the path /data/yashan/wallet as an example.

    ```shell
    $ cd /data/yashan
    $ mkdir wallet
    ```
    > **Warn**:
    >
    > The wallet file involves data security; please plan the storage path appropriately and keep it secure. It is also recommended to store multiple local or offsite backups in a secure location.

- Prepare the values needed for the wallet master key ID (MKID) and the wallet master key (MK) as needed. This document uses the openssl tool to generate the corresponding values as an example:

    ```shell
    # Generate MKID using openssl
    $ openssl rand -hex 16
    1e3eeec2f7cc8af9a919517216e8057d

    # Generate MK using openssl
    $ openssl rand -hex 32
    d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444
    ```

- Ensure that the database user executing the wallet configuration has ADMINISTER KEY MANAGEMENT privilege or the SYSKM role. For granting operations, please refer to [Privilege Management](../../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

    ```sql
    -- Check the current user's privilege information
    SELECT PRIVILEGE FROM USER_SYS_PRIVS;
    -- Check the current user's role information
    SELECT GRANTED_ROLE FROM USER_ROLE_PRIVS;
    ```



#### Operation Steps

1. Configure the wallet root directory [WALLET_ROOT](../../../Reference Manual/Configuration Parameters.html#wallet) parameter for a node in the database (such as the primary database in a standalone primary/standby deployment or any instance in a YAC/Distributed Cluster Deployment).

    ```shell
    # Specify the target node based on the node ID; execute the following command to get the node ID value
    $ yasboot cluster status -c yashandb -d
    
    # This document takes node 1-1 as an example
    $ yasboot node config set -c yashandb --node-id 1-1 -k WALLET_ROOT -v '/data/yashan/wallet'
    ```


2. Restart the database for the configuration to take effect.

    ```shell
    $ yasboot cluster restart -c yashandb -d
    ```

3. Connect to the above node (node 1-1) and check whether the WALLET_ROOT configuration has taken effect.

    ```shell
    $ yasql sales/********@192.168.1.2:1688

    SQL> SHOW PARAMETER WALLET_ROOT

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    WALLET_ROOT                                                      /data/yashan/wallet
    ```

4. Execute the following statement to create the wallet. For syntax details, refer to the [create_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#createkeystore) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    -- Scenario 1: If keystore_location is not specified, the system will automatically create a subdirectory /tde and wallet file under the WALLET_ROOT path
    ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY Your_password;

    -- Scenario 2: If keystore_location is specified, it must be specified as the /tde subdirectory under the WALLET_ROOT path
     ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY Your_password;
    ```

    

    >**Caution**:
    >
    > Please remember the wallet password and back up the wallet promptly. Loss of wallet-related data may render the database unavailable.

5. Execute the following statement to open the wallet. For syntax details, refer to the [open_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#openkeystore) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY Your_keystore_password;
    ```

6. Execute the following statement to create and activate the master key. For syntax details, refer to the [set_key](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#setkey) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    -- This statement must be executed when the database instance is in the OPEN phase
    ADMINISTER KEY MANAGEMENT SET KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
    ```

7. Configure and open the wallet for all standby databases in primary/standby deployments and other instances in YAC/Distributed Cluster Deployment:

    1. Configure the wallet root directory [WALLET_ROOT](../../../Reference Manual/Configuration Parameters.html#wallet) parameter for the other nodes in the database sequentially.

    ```shell    
    # Specify different node IDs in sequence to execute this command until all nodes have been configured
    $ yasboot node config set -c yashandb --node-id 1-2 -k WALLET_ROOT -v '/data/yashan/wallet'   
    ```

   2. Use the Linux command to copy the tde folder and its files from the WALLET_ROOT path of node 1-1 to the WALLET_ROOT path of the other nodes.
    
   3. Restart the database and open the wallet.
    
        ```shell
        # The --wallet-password parameter must be specified
        $ yasboot cluster restart -c yashandb -d --wallet-password Your_keystore_password
        ```

    Configuration is complete, and you can use the [TDE](Transparent Data Encryption) related functionality.



### Third-party Key Management



#### Preparation

- Plan and create a storage path for the wallet file on each database server. The YashanDB install user (i.e., yashan) must have full privileges for the corresponding path. This chapter takes the path /data/yashan/wallet as an example.

    ```shell
    $ cd /data/yashan
    $ mkdir wallet
    ```
    > **Warn**:
    >
    > The wallet file involves data security; please plan the storage path appropriately and keep it secure. It is also recommended to store multiple local or offsite backups in a secure location.

- Prepare the values needed for the wallet master key ID (MKID) and the wallet master key (MK) as needed. This document uses the openssl tool to generate the corresponding values as an example:

    ```shell
    # Generate MKID using openssl
    $ openssl rand -hex 16
    1e3eeec2f7cc8af9a919517216e8057d

    # Generate MK using openssl
    $ openssl rand -hex 32
    d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444
    ```

- Ensure that the database user executing the wallet configuration has ADMINISTER KEY MANAGEMENT privilege or the SYSKM role. For granting operations, please refer to [Privilege Management](../../Data Access Control/Privilege and Role Management/Permission Granting and Revocation).

    ```sql
    -- Check the current user's privilege information
    SELECT PRIVILEGE FROM USER_SYS_PRIVS;
    -- Check the current user's role information
    SELECT GRANTED_ROLE FROM USER_ROLE_PRIVS;
    ```


- A CMK has been created in Tencent Cloud Key Management Service (operational guidance can be referred to in the [Tencent Cloud KMS official documentation](https://cloud.tencent.com/document/product/573/8875)), and the following information has been obtained:

    > **Note**:
    >
    > The supported API version for Tencent Cloud KMS is 2019-01-18 (or other compatible versions). For specific version details, please consult Tencent Cloud official technical support. 

    - The API address of the KMS service, for example `https://kms.tencentcloudapi.com/`

    - keyId: The CMK ID created in the KMS.
    
    - secretId: The cloud API key ID of the Tencent Cloud account that created the root key.
    
    - secretKey: The cloud API key value of the Tencent Cloud account that created the root key.

#### Operation Steps

1. Configure the key management method (parameter [TDE_CONFIGURATION](../../../Reference Manual/Configuration Parameters.html#TDE_CONFIGURATION)) and the wallet root directory (parameter [WALLET_ROOT](../../../Reference Manual/Configuration Parameters.html#wallet)) for a node in the database (such as the primary database in a standalone high availability deployment or any instance in a YAC Deployment).

    ```shell
    # Specify the target node based on the node ID; execute the following command to get the node ID value
    $ yasboot cluster status -c yashandb -d
    
    # This document takes node 1-1 as an example
    $ yasboot node config set -c yashandb --node-id 1-1 -k TDE_CONFIGURATION -v 'KEYSTORE_CONFIGURATION=FILE;KMS=TRUE' -k WALLET_ROOT -v '/data/yashan/wallet'
    ```



2. Restart the database for the configuration to take effect.

    ```shell
    $ yasboot cluster restart -c yashandb -d
    ```

3. Connect to the above node (node 1-1) and check whether the WALLET_ROOT configuration has taken effect.

    ```shell
    $ yasql sales/********@192.168.1.2:1688

    SQL> SHOW PARAMETER WALLET_ROOT

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    WALLET_ROOT                                                      /data/yashan/wallet
    ```

4. Execute the following statement to create the wallet. For syntax details, refer to the [create_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#createkeystore) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    -- Scenario 1: If keystore_location is not specified, the system will automatically create a subdirectory /tde and wallet file under the WALLET_ROOT path
    ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY Your_password;

    -- Scenario 2: If keystore_location is specified, it must be specified as the /tde subdirectory under the WALLET_ROOT path
     ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY Your_password;
    ```

    

    >**Caution**:
    >
    > Please remember the wallet password and back up the wallet promptly. Loss of wallet-related data may render the database unavailable.

5. Execute the following statement to open the wallet. For syntax details, refer to the [open_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#openkeystore) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY Your_keystore_password;
    ```

6. Execute the following statement to create and activate the master key. For syntax details, refer to the [set_key](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#setkey) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    -- This statement must be executed when the database instance is in the OPEN phase
    ADMINISTER KEY MANAGEMENT SET KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
    ```

7. Configure and open the wallet for all standby databases in primary/standby deployments and other instances in YAC/Distributed Cluster Deployment:

    1. Configure the wallet root directory [WALLET_ROOT](../../../Reference Manual/Configuration Parameters.html#wallet) parameter for the other nodes in the database sequentially.

    ```shell    
    # Specify different node IDs in sequence to execute this command until all nodes have been configured
    $ yasboot node config set -c yashandb --node-id 1-2 -k WALLET_ROOT -v '/data/yashan/wallet'   
    ```

   2. Use the Linux command to copy the tde folder and its files from the WALLET_ROOT path of node 1-1 to the WALLET_ROOT path of the other nodes.
    
   3. Restart the database and open the wallet.
    
        ```shell
        # The --wallet-password parameter must be specified
        $ yasboot cluster restart -c yashandb -d --wallet-password Your_keystore_password
        ```

    Configuration is complete, and you can use the [TDE](Transparent Data Encryption) related functionality.



    ```sql
    -- Scenario 1: If keystore_location is not specified, the system will automatically create a subdirectory /tde and wallet file under the WALLET_ROOT path
    ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY "kms://tencent/?password=Your_password&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";

    -- Scenario 2: If keystore_location is specified, it must be specified as the /tde subdirectory under the WALLET_ROOT path
     ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY "kms://tencent/?password=Your_password&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";
    ```

    

    >**Caution**:
    >
    > Please remember the wallet password and back up the wallet promptly. Loss of wallet-related data may render the database unavailable.

5. Execute the following statement to open the wallet. For syntax details, refer to the [open_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#openkeystore) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY Your_keystore_password;
    ```

6. Execute the following statement to create and activate the master key. For syntax details, refer to the [set_key](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#setkey) clause in the ADMINISTER KEY MANAGEMENT statement.

    ```sql
    -- This statement must be executed when the database instance is in the OPEN phase
    ADMINISTER KEY MANAGEMENT SET KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
    ```

7. Configure and open the wallet for all standby databases in primary/standby deployments and other instances in YAC/Distributed Cluster Deployment:

    1. Configure the wallet root directory [WALLET_ROOT](../../../Reference Manual/Configuration Parameters.html#wallet) parameter for the other nodes in the database sequentially.

    ```shell    
    # Specify different node IDs in sequence to execute this command until all nodes have been configured
    $ yasboot node config set -c yashandb --node-id 1-2 -k WALLET_ROOT -v '/data/yashan/wallet'   
    ```

   2. Use the Linux command to copy the tde folder and its files from the WALLET_ROOT path of node 1-1 to the WALLET_ROOT path of the other nodes.
    
   3. Restart the database and open the wallet.
    
        ```shell
        # The --wallet-password parameter must be specified
        $ yasboot cluster restart -c yashandb -d --wallet-password Your_keystore_password
        ```

    Configuration is complete, and you can use the [TDE](Transparent Data Encryption) related functionality.



## Daily Opening of the Wallet

After creating encrypted objects, **the wallet password must be specified to open the wallet during the startup of the database**:

```shell
# The --wallet-password parameter must be specified
$ yasboot cluster start -c yashandb -d --wallet-password Your_keystore_password
```

>**Caution**:
>
> - In Standalone/YAC/Distributed Cluster Deployment, all nodes' wallets must be opened; otherwise, newly added encrypted objects may cause other nodes to fail to apply due to wallet being closed, and in single (primary/standby) deployments, it may also cause NEED REPAIR. In such cases, configure the wallet and restart the database as per the above operation steps.
>
> - Even if all encrypted objects (tablespaces, tables, or columns) are later deleted, the wallet password must still be specified to open the wallet during database startup.

## Backing up the Wallet

Please back up the wallet promptly. Loss of wallet-related data may render the database unavailable.

Perform a backup of the wallet using the [backup_keystore](../../../Development Guide/SQL Reference Manual/SQL Statements/ADMINISTER KEY MANAGEMENT.html#backupkeystore) clause of the `ADMINISTER KEY MANAGEMENT` statement.

```sql
ADMINISTER KEY MANAGEMENT BACKUP KEYSTORE IDENTIFIED BY Your_keystore_password TO '/data/yashan';
```
