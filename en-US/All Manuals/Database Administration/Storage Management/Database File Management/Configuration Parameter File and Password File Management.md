YashanDB creates two important configuration files during product installation: the configuration parameter file and the password file. These files control the system configuration parameters and system user passwords for the database instance they belong to. Deleting or corrupting these two files will result in the inability to start or log into the respective instance.

## Configuration Parameter File

The configuration parameter file is a text file named yasdb.ini, located in the $YASDB_DATA/config directory. The initial configuration parameters for the database specified during product installation will be saved in this file. Any non-memory modifications to the configuration parameters during subsequent database operations will also be persisted in this file.

>**Note**:
>
>This article takes Standalone Deployment as an example. The $YASDB_DATA path varies for different deployment types; please refer to the actual situation.

```ini
$ cat yasdb.ini 
_ENABLE_TAC=FALSE
_ENABLE_LSC=FALSE
_ENABLE_EPC=FALSE
LISTEN_ADDR=0.0.0.0:1688
DB_BLOCK_SIZE=8K
DATA_BUFFER_SIZE=256M
CHARACTER_SET = UTF8
CONTROL_FILES = ('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1', '/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2', '/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3')
```

> **Caution**: 
>
>In general, it is not recommended to edit the database configuration file directly to modify configuration parameters, as this may lead to unpredictable anomalies. If modification is necessary, please use SQL commands. For detailed operations, refer to [ALTER SYSTEM](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM).

## Password File

The password file is a text file named yasdb.pwd, which cannot be edited directly. Its path is controlled by the PASSWORD_FILE parameter.

***Example***

```sql
show parameter PASSWORD_FILE;

NAME               VALUE                   
------------------ ----------------------- 
PASSWORD_FILE      ?/instance/yasdb.pwd   
```

Here is an example of the contents of yasdb.pwd:

***Example***

```ini
$ cat yasdb.pwd
M'´iSYSS:9E276A5EFA869D8DD05E6CE27CC8430194D04451D6FE9EBCAA7BE0A933800084621C2654CA20D47DFFC9
```

The content of this file is encrypted information. Although it is a text file, its contents cannot be modified directly. If you need to change the SYS user's password, you can do so with the following commands:

***Example***

```shell
$ yaspwd file=yasdb.pwd
$ yaspwd file=yasdb.pwd input_file=yasdb_input.pwd sys=y
```

> **Note**: 
>
>A new password file can only be generated when the yasdb.pwd file does not exist. Therefore, before modifying the password, you should first delete or rename yasdb.pwd. For detailed operations, refer to the tool manual [yaspwd](../../../Tools Guide/yaspwd).