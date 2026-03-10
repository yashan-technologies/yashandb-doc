General Description
----

ADMINISTER KEY MANAGEMENT provides a unified key management interface for Transparent Data Encryption, which is used to manage the wallet and master keys.

This statement does not apply to ISC Distributed Cluster Deployment.

>**Caution**:
>
> Please remember the password for the [wallet](#createkeystore) and back it up in a timely manner; loss of wallet-related data may render the database unavailable.

Statement Definition
----

**administer key management::=**

```ebnf+diagram
syntax::=  ADMINISTER KEY MANAGEMENT (keystore_management_clauses
|key_management_clauses)
```

**[keystore\_management\_clauses](#keystoremanagementclauses)::=**

```ebnf+diagram
syntax::= (create_keystore
| open_keystore
| close_keystore
| backup_keystore)
```

**[create\_keystore](#createkeystore)::=**

```ebnf+diagram
syntax::= CREATE KEYSTORE [ "'" keystore_location "'" ] IDENTIFIED BY keystore_password
```

**[open\_keystore](#openkeystore)::=**

```ebnf+diagram
syntax::= SET KEYSTORE OPEN
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password ) 
```

**[close\_keystore](#closekeystore)::=**

```ebnf+diagram
syntax::= SET KEYSTORE CLOSE
  [ IDENTIFIED BY ( EXTERNAL STORE | keystore_password ) ] 
```

**[backup_keystore](#backupkeystore)::=**

```ebnf+diagram
syntax::= BACKUP KEYSTORE [ USING "'" backup_identifier "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  [ TO "'" keystore_location "'" ] 
```

**[key\_management\_clauses](#keymanagementclauses)::=**

```ebnf+diagram
syntax::= ( set_key
  | create_key
  | use_key
  | set_key_tag
  ) 
```

**[set\_key](#setkey)::=**

```ebnf+diagram
syntax::= SET [ ENCRYPTION ] KEY [ mkid_mk | mk ]
  [ USING TAG "'" tag "'" ]
  [ USING ALGORITHM "'" encrypt_algorithm "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[create\_key](#createkey)::=**

```ebnf+diagram
syntax::= CREATE [ ENCRYPTION ] KEY [ mkid_mk | mk ]
  [ USING TAG "'" tag "'" ]
  [ USING ALGORITHM "'" encrypt_algorithm "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[use\_key](#usekey)::=**

```ebnf+diagram
syntax::= USE [ ENCRYPTION ] KEY "'" key_id "'"
  [ USING TAG "'" tag "'" ]
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

**[set_key_tag](#setkeytag)::=**

```ebnf+diagram
syntax::= SET TAG "'" tag "'" FOR "'" key_id "'" 
  [ FORCE KEYSTORE ]
  IDENTIFIED BY ( EXTERNAL STORE | keystore_password )
  WITH BACKUP [ USING "'" backup_identifier "'" ] 
```

<span id="keystoremanagementclauses" name="keystoremanagementclauses" class="yaslink"></span>

### 1. keystore\_management\_clauses

This statement is used for wallet management operations, including creating new wallets and opening or closing existing wallets.

<span id="createkeystore" name="createkeystore" class="yaslink"></span>

#### 1.1. create\_keystore

This statement is used to create a wallet. Only one wallet is allowed to be created in the same directory.

Before creating a wallet, you should first choose whether to use local key management or third-party key management as needed:

- [Local key management](#local): Persist the master key in a local wallet file on the database server.

- [Third-party key management](#kms): Integrate with a key management service provided by a third party, which protects the master key. Currently, only Tencent Cloud KMS is supported. To use third-party key management, you must first modify the configuration parameter [TDE_CONFIGURATION](../../All Manuals/Reference Manual/Configuration Parameters.html#TDE_CONFIGURATION) to `KEYSTORE_CONFIGURATION=FILE;KMS=TRUE`.

##### 1.1.1. keystore\_location

Specifies the path information of the wallet, which must have read and write privileges. Creating a wallet will automatically generate the ewallet.p12 file in that path.

There is no absolute order constraint between creating the wallet and configuring the [WALLET_ROOT](../../All Manuals/Reference Manual/Configuration Parameters.html#wallet) parameter, but the following path requirements apply to the wallet file:

- If the WALLET_ROOT parameter is configured before creating the wallet:

  - The keystore_location can be omitted; the system will default to create a subdirectory `tde` under the WALLET_ROOT path and generate the ewallet.p12 file in that subdirectory.

  - If specifying keystore_location, it must be the `/tde` subdirectory of the path specified by parameter WALLET_ROOT.

- If creating the wallet before configuring the WALLET_ROOT parameter:

  The keystore_location must be specified; it is recommended to specify it as the `/tde` subdirectory of the planned path for parameter WALLET_ROOT. Otherwise, after creating the wallet, the relevant files must be moved to the `/tde` subdirectory of the path specified by parameter WALLET_ROOT before [opening the wallet](#openkeystore).

##### 1.1.2. IDENTIFIED BY keystore\_password

This statement is used to specify the wallet authentication credentials.

<span id="local" name="local" class="yaslink"></span>

**Local key management**

When creating a wallet with local key management, the authentication information is the password of the wallet file ewallet.p12.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Configure the WALLET_ROOT parameter, must restart the database for it to take effect
ALTER SYSTEM SET WALLET_ROOT='/data/yashan/wallet' scope=spfile;

-- Restart the database to apply the configuration changes.​

-- Scenario 1: Create a wallet without specifying keystore_location, the system will automatically create subdirectory /tde
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY admin123;

-- Scenario 2: Create a wallet specifying keystore_location, must specify to the tde level
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY admin123;
```

<span id="kms" name="kms" class="yaslink"></span>

**Third-party key management**

When creating a wallet that uses third-party key management, the authentication information is the KMS-related configuration, formatted as `<schema>://<host>/<path/to/resource>?<query>#<hash>`.

|Configuration Item         |Description              |
|--------------------|--------------------------------------|
| \<schema\> | Fixed as kms. |
| \<host\>  |Specifies the KMS type. Currently, only Tencent Cloud KMS is supported. The value can only be "tencent", case-insensitive. |
| \<path/to/resource\>  |Reserved configuration, currently no need to configure. |
| \<query\>  |  Configuration key-value pairs, separated by "&". The key-value pairs include: <br/> - password: The password for the wallet file ewallet.p12. <br/> - skipCheck: Whether to skip the KMS self-check. The value is case-insensitive, and can be [true&#124;false]. The default is FALSE, meaning it will not skip. If the self-check is not skipped, the KMS service configuration will be checked when opening the wallet to test if a certain ciphertext can be decrypted normally. If decryption is successful, the KMS service is considered correctly configured. <br/> - KMS service integration information: For example, Tencent Cloud KMS service's keyId (the CMK ID created in the KMS), secretId (the cloud API key ID of the Tencent Cloud account that created the root key), and secretKey (the cloud API key value of the Tencent Cloud account that created the root key).  |
| \<hash\>              | The API address of the KMS service, for example `https://kms.tencentcloudapi.com/` |


***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
-- Configure the TDE_CONFIGURATION and WALLET_ROOT parameter, must restart the database for it to take effect
ALTER SYSTEM SET TDE_CONFIGURATION='KEYSTORE_CONFIGURATION=FILE;KMS=TRUE' WALLET_ROOT='/data/yashan/wallet' scope=spfile;

-- Restart the database to apply the configuration changes.​

-- Scenario 1: Create a wallet without specifying keystore_location, the system will automatically create subdirectory /tde
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE IDENTIFIED BY "kms://tencent/?password=admin123&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";

-- Scenario 3: Create a wallet specifying keystore_location, must specify to the tde level
ADMINISTER KEY MANAGEMENT CREATE KEYSTORE '/data/yashan/wallet/tde' IDENTIFIED BY "kms://tencent/?password=admin123&keyId=6xxxxxxx-xxxx-xxxx-xxxx-5xxxxxxxxc09&secretId=AKID********************************&secretKey=********************************#https://kms.tencentcloudapi.com/";
```

<span id="openkeystore" name="openkeystore" class="yaslink"></span>

#### 1.2. open\_keystore

This statement is used to open the wallet the `/tde` subdirectory of the path specified by parameter WALLET_ROOT, and the the wallet authentication credentials must be specified through IDENTIFIED BY keystore_password.

The usage rules for this statement are as follows:

- Ensure that the WALLET_ROOT parameter is correctly configured, the ewallet.p12 and other wallet-related files have been generated and stored in the `/tde` subdirectory of the path specified by parameter WALLET_ROOT.

- In primary/standby deployments, the wallet must be opened in all primary and standby databases before starting the database to the MOUNT or OPEN phase; otherwise, encryption log apply failures may occur, rendering the standby database unavailable. If this scenario occurs, an error "wallet is not open" will be logged; the corresponding standby database needs to be connected to open the wallet.

- In YAC/Distributed Cluster Deployment, all instances must open the wallet before starting the database to the MOUNT or OPEN phase; otherwise, encryption log apply failures may occur, making the backup node unavailable. If this scenario occurs, an error "wallet is not open" will be logged; the corresponding instance needs to be connected to open the wallet.

FORCE KEYSTORE and IDENTIFIED BY EXTERNAL STORE are only used for syntax compatibility and have no practical meaning.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY Your_keystore_password;
```

<span id="closekeystore" name="closekeystore" class="yaslink"></span>

#### 1.3. close\_keystore

This statement is used to close the wallet in the subdirectory `tde` of the WALLET_ROOT, and the wallet authentication credentials must be specified through IDENTIFIED BY keystore_password.

IDENTIFIED BY EXTERNAL STORE is only used for syntax compatibility and has no practical meaning.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ADMINISTER KEY MANAGEMENT SET KEYSTORE CLOSE IDENTIFIED BY Your_keystore_password;
```

<span id="backupkeystore" name="backupkeystore" class="yaslink"></span>

#### 1.4. backup\_keystore

This statement is used to back up the wallet in the subdirectory `tde` of the WALLET_ROOT.

You need to [open the wallet](#openkeystore) before executing this statement, or force the wallet to open by using the FORCE KEYSTORE keyword; otherwise, the statement will fail.

##### 1.4.1. USING 'backup\_identifier'

Specify an identifier for the wallet backup file (optional).

##### 1.4.2. TO 'keystore\_location'

Specify the backup path for the wallet (requires read/write permissions on the path). It is optional - if omitted, backups will be created in the same directory as the original file.

##### 1.4.3. FORCE KEYSTORE

Specifying this keyword allows the wallet to be forced open, and the wallet remains open after the current clause ends.

If the wallet is not [opened](#openkeystore), the FORCE KEYSTORE keyword must be specified.

##### 1.4.4. IDENTIFIED BY keystore\_password

This statement is used to specify the authentication credentials for the target wallet.

IDENTIFIED BY EXTERNAL STORE is only used for syntax compatibility and has no practical meaning.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ADMINISTER KEY MANAGEMENT BACKUP KEYSTORE IDENTIFIED BY Your_keystore_password;
```

<span id="keymanagementclauses" name="keymanagementclauses" class="yaslink"></span>

### 2. key\_management\_clauses

This statement is used to manage the master key, including [creating and activating the master key](#setkey), [creating but not activating the master key](#createkey), [activating the master key](#usekey), and [setting tag for the master key](#setkeytag).

You need to [open the wallet](#openkeystore) before executing this statement, or force the wallet to open by using the FORCE KEYSTORE keyword; otherwise, the statement will fail.

<span id="setkey" name="setkey" class="yaslink"></span>

#### 2.1. set\_key

This statement is used to set (equivalent to creating + activating) the first master key in the wallet in the subdirectory `tde` of the WALLET_ROOT, or to rotate (update) an existing master key.

The usage rules for this statement are as follows:

- This statement must be executed when the database is in the OPEN phase.

- In YAC/Distributed Cluster Deployment, if the key paths are the same among multiple instances, after updating the master key on one instance, the wallet on other instances must be closed and reopened to ensure consistency of the master key across all instances.

ENCRYPTION and IDENTIFIED BY EXTERNAL STORE are only used for syntax compatibility and have no practical meaning.

>**Note**:
>
> If a power failure occurs during the execution of the SET KEY statement, it may result in the wallet file being partially written, which may render the database unable to start. When this issue occurs, the database ensures that the backup key file has been completed before writing the wallet file. You need to manually restore the backup key file to the currently used file.

##### 2.1.1. mkid\_mk|mk|

Used to specify the value required for the wallet master key ID (MKID) and the value required for the wallet master key (MK); both MKID and MK may be specified, or only MK may be specified, or both may be omitted.

mkid_mk indicates that both MKID and MK are specified, formatted as `MKID:MK`.

mk indicates that only MK is specified, and the database will automatically generate an MKID to track the master key with the specified MK.

Omitting indicates that both MKID and MK are not specified, and the database will automatically generate both MKID and MK.

- MKID is a 16-byte random value represented in hexadecimal string format and cannot duplicate an existing MKID in the database.

- MK is a 32-byte random value represented in hexadecimal string format.

If specifying MKID or MK, please ensure the values are valid, and it is recommended to use the openssl tool to generate MKID and MK.

- If the MKID is invalid (e.g., length error, or a string of zeros), an error will be raised: invalid master key identifier or master key value.

- If MKID is duplicated, an error will be raised: master key identifier exists in the keystore.

- If MKID is invalid or MK is invalid, an error will be raised: invalid master key identifier or master key value.

##### 2.1.2. USING TAG 'tag'

This statement is used to associate a tag with the master key, which can be a maximum of 4000 characters.

##### 2.1.3. USING ALGORITHM 'encrypt\_algorithm'

This statement is used to specify the encryption algorithm for the master key, only supporting AES256.

##### 2.1.4. FORCE KEYSTORE

Specifying this keyword allows the wallet to be forced open, and the wallet remains open after the current clause ends.

If the wallet is not [opened](#openkeystore), the FORCE KEYSTORE keyword must be specified.

##### 2.1.5. IDENTIFIED BY keystore\_password

This statement is used to specify the authentication credentials for the target wallet.

##### 2.1.6. WITH BACKUP [USING 'backup\_identifier']

Specifying this keyword is used to back up the wallet before creating a new master key; it must be filled out, otherwise, an error will be raised.

The backup file name defaults to `ewallet_<timestamp>.p12`, where the timestamp is expressed in UTC format. Specifying `USING 'backup_identifier'` can customize the backup file name format to `ewallet_<timestamp>_<backup_identifier>.p12`.

The backup file is saved in the same directory as the original ewallet.p12 file.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```shell
-- Use the openssl tool to generate MKID
$ openssl rand -hex 16
1e3eeec2f7cc8af9a919517216e8057d

-- Use the openssl tool to generate MK
$ openssl rand -hex 32
d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444

-- After logging into the database, set the master key
SQL> ADMINISTER KEY MANAGEMENT SET KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="createkey" name="createkey" class="yaslink"></span>

#### 2.2. create\_key

This statement is used to create a new master key. After creation, the master key cannot be used directly but must be activated using the [use_key](#usekey) statement.

The syntax and rules are the same as [set_key](#setkey).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
ADMINISTER KEY MANAGEMENT CREATE KEY '1e3eeec2f7cc8af9a919517216e8057d:d8ab781a44b864099a5e607a2063b265d7703f802fcfe7e8334cd8776da44444' USING TAG 'myTag' IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="usekey" name="usekey" class="yaslink"></span>

#### 2.3. use\_key

This statement is used to activate an already existing master key; key_id must be specified as the ID of the target master key (which can be obtained by querying the KEY_ID field of the [V$ENCRYPTION_KEYS](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$ENCRYPTION_KEYS.html) view).

Other syntax and rules are the same as [set_key](#setkey).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             myTag

ADMINISTER KEY MANAGEMENT USE ENCRYPTION KEY 'AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA' USING TAG 'newTag'  IDENTIFIED BY Your_keystore_password WITH BACKUP;
```

<span id="setkeytag" name="setkeytag" class="yaslink"></span>

#### 2.4. set\_key\_tag

This statement is used to assign or update a tag for an existing master key; key_id must be specified as the ID of the target master key (which can be obtained by querying the KEY_ID field of the [V$ENCRYPTION_KEYS](../../All Manuals/Reference Manual/System Views/Dynamic Performance Views/V$ENCRYPTION_KEYS.html) view).

Other syntax and rules are the same as [set_key](#setkey).

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             myTag

ADMINISTER KEY MANAGEMENT SET TAG 'mynewTag' FOR 'AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA' USING TAG 'newTag' IDENTIFIED BY Your_keystore_password;

SELECT KEY_ID,TAG FROM V$ENCRYPTION_KEYS;

KEY_ID                                                           TAG
---------------------------------------------------------------- ----------------------------------------------------------------
AR4+7sL3zIr5qRlRchboBX0AAAAAAAAAAAAAAAAAAAAAAAAAAAAA             mynewTag
```
