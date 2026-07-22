YashanDB creates two important configuration files during product installation: the configuration parameter file and the password file. These files control the system configuration parameters and system user passwords for the database instance they belong to. Deleting or corrupting these two files will result in the inability to start or log into the respective instance.

## Configuration Parameter File

The configuration parameter file is a text file named yasdb.ini, located in the $YASDB_DATA/config directory. The initial configuration parameters for the database specified during product installation will be saved in this file. Any non-memory modifications to the configuration parameters during subsequent database operations will also be persisted in this file.

>**Caution**:
>
> **Do not edit** the configuration parameter file directly to avoid unpredictable exceptions. If you need to adjust parameter configurations, use the [ALTER SYSTEM](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM) statement.
>
> In YAC Deployment or Distributed Cluster Deployment, to ensure that some parameters remain consistent across multiple instances, an additional parameter file is retained in the config path of the YFS system disk group. This file is mainly used to record such parameter information. The configuration recorded in this file has higher priority than files with the same name in the local path $YASDB_DATA/config on the database server.

## Password File

The password file is a text file named yasdb.pwd, which cannot be edited directly. Its path is controlled by the PASSWORD_FILE parameter.

***Example***

```sql
SQL> show parameter PASSWORD_FILE;

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
