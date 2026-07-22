## Create Installation User

To improve business isolation and reduce operational errors, it is recommended to create an independent installation user for the YashanDB product. 

Please log in as root user or a user with sudo privileges to **all servers** to perform the following operations.

1. Create an account and set a password.

    In this installation manual, yashan is used as the database installation user name.

    ```shell
    # useradd -d /home/yashan -m yashan
    # passwd yashan
    ```

2. Create a new YASDBA user group and add the installation user to this group.

    ```shell
    # groupadd YASDBA
    # usermod -aG YASDBA yashan
    ```

3. (Optional) Configure the installation user for passwordless sudo.

	Certain specific functions (such as creating resource groups, deploying in CDB mode, and YAC SCAN) require the database user to have sudo permissions. If you do not expect to use these functions, you can skip this operation.

    ```shell
    # visudo

    ## Add the following content at the end of the file, save and exit
    yashan ALL=(ALL)NOPASSWD:ALL
    ```

## Create Directories

YashanDB adopts a cluster multi-server parallel installation mode, requiring only logging in as root user or a user with sudo privileges to **a certain server** to create the installation directory and authorize the installation user.

Before installing the YashanDB server, you need to log in as root user or a user with sudo privileges to **all servers** to create the following other directories and authorize the installation user.

The installation user must have full privileges on these directories.

### Installation Directory

This document uses 192.168.1.2 as the installation server and /home/yashan/install as the installation directory.

```shell
# mkdir -p /home/yashan/install
# chown -R yashan:yashan /home/yashan/install
```

<span id="HOMEDirectory" name="HOMEDirectory"></span>
### HOME Directory and DATA Directory

- **HOME Directory**: The product directory for YashanDB, used to store the commands provided by YashanDB, libraries required for database operation, and various key components. In the installation operation, this directory will serve as the value of the --install-path parameter. After installation, YashanDB will generate $YASDB_HOME in this directory according to the rules.

- **DATA Directory**: The data directory for YashanDB, used to store various system data files of the database, some log files (please refer to [Log Management](../../../Database Administration/Operation Monitoring/Log Management/00Log Management) for details), and configuration files. User data is also stored in this directory by default. However, for YACs, all data files and redo files must be stored on shared storage, and the DATA directory will only be used to store instance runtime-related configuration files, log files, and other data. In the installation operation, this directory will serve as the value of the --data-path parameter. After the installation, YashanDB will generate $YASDB_DATA in this directory according to the rules.

    > **Note**:
    >
    > If the DATA directory path is too long, it may affect the normal operation of certain features, such as [UDS local connections](../../../Product Security/Connection Management/00Connection Management) and other UDS-based features. It is recommended that the path length does not exceed 64 bytes.

In this installation manual, the sample values for HOME and DATA directories are planned under /data/yashan. You can execute the following commands to create the corresponding paths and grant permissions:

```shell
## Create the /data/yashan directory, HOME directory, and DATA directory
# mkdir -p /data/yashan
# mkdir -p /data/yashan/yasdb_home
# mkdir -p /data/yashan/yasdb_data

## Direct permission grant based on the parent directory
# chown -R yashan:yashan /data/yashan
```

<span id="run_log_path" name="run_log_path"></span>

### Run Log Directory

The run log directory of YashanDB is used to store log files generated during the database operation, including run.log and slow.log of the database, as well as logs from yasom and yasagent.

To avoid interference between run logs and data file IO, it is recommended to configure the database run log directory on a different physical disk than the DATA directory. Additionally, the run log directory can be planned according to the following scenarios:

- Unified storage according to planning

    1. Plan a directory to store run.log, slow.log, and logs from yasom and yasagent.

        ```shell
        # mkdir -p /data/yashan/log
        # chown -R yashan:yashan /data/yashan/log
        ```
    
    2. In the installation operation, this directory will serve as the value of the --data-path parameter.

- Separated storage according to planning

    1. Plan different directories to separately store run.log, slow.log, and logs from yasom and yasagent.
    
    2. In the installation operation, you can use one of these directories (e.g., the planned directory for yasom and yasagent logs) as the value of the --log-path parameter. After generating the [configuration file](../../../Tools Guide/yasboot/Configuration Files/00Configuration Files){cluster_name}.toml, modify the parameters related to the other two types of log directories as needed before executing the subsequent installation deployment operations. Alternatively, you can modify the corresponding [configuration parameters](../../../Reference Manual/Configuration Parameters) to adjust the storage locations of run.log and slow.log after the installation is completed.

- Use default mechanism

    - If the run log directory is not planned, YashanDB will create a subdirectory log under the HOME directory by default (according to the example in this manual, it would be /data/yashan/yasdb_home/log) to store run.log, slow.log, as well as logs from yasom and yasagent.
    
    - In the installation operation, there is no need to specify the --log-path parameter.

## Download Package

Before downloading the software package, please log in to the installation server as the yashan user (this document uses 192.168.1.2 as the installation server).

Navigate to the created installation directory (this manual uses /home/yashan/install as the installation directory):

```shell
$ cd /home/yashan/install
```

Please [download (https://download.yashandb.com/download)](https://download.yashandb.com/download) the software package according to the required usage scenario, or contact our technical support to obtain the software package.

For convenience in describing the YashanDB installation and deployment process, we will use the `yashandb-{version_number}-linux-x86_64.tar.gz` installation package as an example.

After downloading, please extract the file:

```shell
$ tar zxf yashandb-{version_number}-linux-x86_64.tar.gz
```

Execute the following commands to check if the installation command is working properly:

```shell
$ ll ./bin/yasboot
-rwxrwxr-x 1 yashan yashan 9377280 Sep 26 01:32 yasboot
$ ll ./om/bin/yasom
-rwxrwxr-x 1 yashan yashan 9377280 Sep 26 01:32 yasom
```