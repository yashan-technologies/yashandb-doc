In YashanDB, there are two important types of configuration files: the configuration parameter file and the password file. The former controls the system configuration parameters of the database instance it belongs to, while the latter manages the passwords of sys user. Damage to or absence of these two types of files will prevent the instance from starting up or being logged into. 

## Configuration Parameter File

In YashanDB, the configuration parameter files can be divided into server parameter files and parameter files. When a database instance starts, it needs to be initialized with parameters from these types of files.

> **Caution**: 
>
> **Do not** edit the configuration parameter file directly to avoid unpredictable exceptions. If you need to adjust parameter configurations, use the [ALTER SYSTEM](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM) statement.
>
> In YAC Deployment or Distributed Cluster Deployment, to ensure that some parameters remain consistent across multiple instances, an additional parameter file is retained in the config path of the YFS system disk group. This file is mainly used to record such parameter information. The configuration recorded in this file has higher priority than files with the same name in the local path $YASDB_DATA/config on the database server.

|Characteristics   |Server Parameter File      |Parameter File      |
| --------------------- | ---------------------------- | ----------------------------- |
| Creation Method       | After installation, users create it as needed by executing the [CREATE SPFILE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE SPFILE) statement based on the parameter file | Automatically created during installation. <br />Can be updated as needed by executing the [CREATE PFILE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE PFILE) statement to overwrite based on the server parameter file |
| File Path             | The default path is $YASDBDATA/config<br />In YAC Deployment, an additional file will be generated in YFS. The default path is +DG0/config, which is used to record parameter configurations that require consistency across multiple instances (where CLUSTER_CONSISTENT = TRUE in the V$PARAMETER view) | The same as the server parameter file                        |
| File Name            | yasdb.spfile            | yasdb.ini                 |
| File Format           | Binary                 | Text<br />Can be directly read and written, and has relatively low security |
| Usage Priority        | High<br />When the database instance starts, it first attempts to read the yasdb.spfile file. Only when this file does not exist or the verification fails will it read the yasdb.ini file | Low                  |
| Parameter Persistence | When executing [ALTER SYSTEM](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER SYSTEM) and specifying scope as spfile or both to modify parameters, the parameter values will be persisted to the currently used file | The same as the server parameter file           |
| Others  | When starting a database instance using the yasdb.spfile file, if the [SSL Trusted Channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel) (SSL_ENABLE = ON and ENCRYPT_TYPE = SSL) has been enabled, an additional check will be performed on the consistency between the server-side certificate file-related configurations and the records in the yasdb.spfile file. If the consistency check fails, the startup will fail directly (it will not switch to the yasdb.ini file) | -  |

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
