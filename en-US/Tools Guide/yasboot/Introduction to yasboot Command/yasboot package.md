## package se gen

The command is used to generate a configuration file for Standalone Deployment based on the input options.

|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **Server Configuration Parameters** |  |
| *-u,--username*         | SSH username for the server                  |
| *--groupname*           | User group name, defaults to `username`                  |
| *-p,--password*         | SSH login password          |
| *-N,--no-password*      | SSH passwordless login             |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username<br />It is used to execute commands that require sudo privileges, such as creating cgroup directories |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password      |
| *--host*                | Server connection information expression           |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`        |
| *--port*                | SSH connection port of the server   |
| **Database Basic Configuration Parameters** |  |
| -c,--cluster       | Name of the generated cluster (required parameter)           |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)         |
| *--data-path*           | Data path of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)     |
| *--log-path*            | Run log path, storing database run.log and slow.log, as well as logs for yasom and yasagent                  |
| *--begin-port*          | Starting port, defaults to 1688|
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)              |
| *--replica-cidr*        | Primary-standby replication link address (defaults to the same IP segment)              |
| *--table-type*          | Main business table type, optional [HEAP,TAC,LSC], defaults to HEAP  |
| *--enable-pluggable-database*  | Whether to deploy as a CDB, defaults to false<br/>When specifying this parameter, the -m (or --mode) parameter cannot be used simultaneously to specify the syntax mode. The PDB's syntax mode can be specified during creation  |
| *--create-cgroup*       | Whether to create the resource management cgroup directory. To create it, sudo privileges are required, and you need to specify at least one of the parameter pairs, either -u, -p or -su, -sp <br/>Created by default when installing a CDB, not created by default for a non-CDB    |
| *--cgroup-path*         | Customize the resource management cgroup directory. When not specified, it defaults to `/sys/fs/cgroup` |
| *-m, --mode*            | Syntax mode of the database, options [yashan,mysql]<br/>* yashan: indicates deployment in yashan mode, defaults to this value when omitted, cannot directly switch to mysql mode after installation in yashan mode<br/>* mysql: indicates deployment in mysql mode     |
| **Node Scale Parameters** |  |
| *--node*                | Scale of standalone db group and node deployment, defaults to 1             |
| *--cascade-node*        | Number of Cascade standby nodes |
| *--cascade-parent*      | Index of the Cascade binding standby node                   |
| *--group*               | Number of standalone groups, defaults to 1                  |
| *--standby-node*        | Number of nodes in the standalone standby group                    |
| **Database Performance Parameters** | If these parameters are not specified during installation, after the installation is completed, they can be adjusted as needed by calling [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM).OPTIMIZE |
| *--recommend-param*     | Whether to enable recommended parameter functionality                        |
| *--memory-limit*        | The upper limit of the percentage of server memory available for the database to use, defaults to 80. The upper limit of available capacity is calculated as `total server memory * memory-limit`<br />Must be used with --recommend-param |
| *--cpu-limit*           | The upper limit of the percentage of the server's CPU that can be used by the database, defaults to 100. The upper limit of available capacity is calculated as `total server CPU * cpu-limit`<br />Must be used with --recommend-param  |
| *--scene*              | Scenario for parameter tuning, defaults to NORMAL and can only be NORMAL<br /> Must be used with --recommend-param |
| *--scale-factor*        | Data scale for test scenarios such as TPCH (unit: GB), defaults to 100<br />Must be used with --recommend-param |
| **Other Parameters** |  |
| *-h,--help*          | View help information for the current command    |
| *--ssl-protocol*        | Specifies the protocol for configuring database [client <-> server trusted channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel), currently only SSL is supported  <br> Must be used with `--ssl-path`                   |
| *--ssl-path*            | Specifies the storage path for SSL-related certificates, must be used with `--ssl-protocol` <br/> When installing the database, all files under this path will be copied to the $YASDB_HOME/ssl path on each server                       |
| *--no-add-yasdba*       | Do not add the installer user to the YASDBA user group <br> **Not recommended**. After specifying this option, the installation user will not be able to log in to the database without password through [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication)          |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy|
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1      |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4            |
| *-o, --output*          | Output path for configuration file   |
| *--dev*                 | For internal development use only  |
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions/LSFA_LISTAGG) function<br>* s3: Install plugins for S3 bucket related functionality<br>* udf: Install plugins for [UDF](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions/XML Function/00XML Function) function |


<span id="ce" name="ce"></span>

## package ce gen

The command is used to generate a configuration file for YAC Deployment or Distributed Cluster Deployment based on the input options.


|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **Server Configuration Parameters** |  |
| *-u,--username*         | SSH username for the server                  |
| *--groupname*           | User group name, defaults to `username`                  |
| *-p,--password*         | SSH login password          |
| *-N,--no-password*      | SSH passwordless login             |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username<br />It is used to execute commands that require sudo privileges, such as creating cgroup directories |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password      |
| *--host*                | Server connection information expression           |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`        |
| *--port*                | SSH connection port of the server  ||
| *--cn-ip*                  | Distributed Cluster Deployment CN IP address, separate multiple IPs with a comma `,`            |
| *--dn-ip*                  | Distributed Cluster Deployment DN IP address, separate multiple IPs with a comma `,`            |
| **Database Basic Configuration Parameters** |  |
| -c,--cluster       | Name of the generated cluster (required parameter)           |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)         |
| *--data-path*           | Data path of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)     |
| *--log-path*            | Run log path, storing database run.log and slow.log, as well as logs for yasom and yasagent                  |
| *--begin-port*          | Starting port, defaults to 1688|
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)              |
| *--replica-cidr*        | Primary-standby replication link address (defaults to the same IP segment)             ||
| *--disk-found-path*     | Disk discovery path in storage network (defaults to /dev/yfs)          |
| *--system-data*         | System disk in storage network must have their paths under the disk discovery path, with multiple system disk information with a comma `,`       |
| *--data*                | Data diskin storage network must have their paths under the disk discovery path, with multiple data disk information with a comma `,`         |
| *-fg, --failgroup*      | Number of failure groups in YAC/Distributed Cluster disk group (defaults to 1)              |
| *--inter-cidr*          | Network communication link address between instances within a YAC/Distributed Cluster (defaults to the same IP segment)|
| *--public-network*                | Public network subnet of YAC, in format subnet/subnet_mask[/network_interface_name], where the network interface name is optional, for example: 192.168.1.0/24      |
| *--vips*                | VIP configuration information for YAC, in the format IP_address/subnet_mask[/network_interface_name], for example: 192.168.1.62/255.255.255.0/ens192. If it is not possible to ensure that all servers in the same cluster have consistent public network interface names, the network interface name must be omitted. --vips must be used with --public-network parameter, and the IP address should belong to the public network. The number of VIPs should match the number of nodes, and multiple VIP configurations are separated by commas `,`      |
| *--scanname*                | SCAN domain name of YAC, must be used with --public-network parameter    |
| *--trtype*               | NVMe communication protocol mode, rdma or tcp supported |
| *--enable-pluggable-database*  | Whether to deploy as a CDB, defaults to false<br/>When specifying this parameter, the -m (or --mode) parameter cannot be used simultaneously to specify the syntax mode. The PDB's syntax mode can be specified during creation  |
| *--create-cgroup*       | Whether to create the resource management cgroup directory. By default, it is not created. To create it, sudo privileges are required, and you need to specify at least one of the parameter pairs, either -u, -p or -su, -sp    |
| *--cgroup-path*         | Customize the resource management cgroup directory. When not specified, it defaults to `/sys/fs/cgroup` |
| *-m, --mode*            | Syntax mode of the database, options [yashan,mysql]<br/>* yashan: indicates deployment in yashan mode, defaults to this value when omitted, cannot directly switch to mysql mode after installation in yashan mode<br/>* mysql: indicates deployment in mysql mode     |
| **Node Scale Parameters** |  |
| *--node*                | * In YAC Deployments, specifies the number of instances in a single cluster, defaulting to 2<br/>* In Distributed Cluster Deployment, specifies the number of CN nodes in a single cluster, defaulting to 2. CN nodes will be created on the server specified by `--cn-ip` |
| *--group*               | Number of YAC groups, default quantity is 1                  |
| *--standby-node*        | Number of instances in standby cluster during Primary/Standby group deployment, defaults to 2       |
| *--node-host-map* | When deploying a YAC in a test environment, the allocation relationship between database instances and servers can be specified through a configuration file<br />The requirements for the file and its content are as follows:<br />* The file name suffix must be `.toml`<br />* The content format is `server_name='instance_node_ID1,…,instance_node_IDn'`, for example, `host0001='1-1,1-2'`. One server is specified per line<br /><br />**Not recommended** for use in a production environment. By default, one instance is allocated on each server in the order of the server list specified by the --ip parameter |
| **Database Performance Parameters** | If these parameters are not specified during installation, after the installation is completed, they can be adjusted as needed by calling [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM).OPTIMIZE |
| *--recommend-param*     | Whether to enable recommended parameter functionality                        |
| *--memory-limit*        | The upper limit of the percentage of server memory available for the database to use, defaults to 80. The upper limit of available capacity is calculated as `total server memory * memory-limit`<br />Must be used with --recommend-param |
| *--cpu-limit*           | The upper limit of the percentage of the server's CPU that can be used by the database, defaults to 100. The upper limit of available capacity is calculated as `total server CPU * cpu-limit`<br />Must be used with --recommend-param  ||
| *--scene*              | Scenario for parameter tuning, defaults to NORMAL and can only be NORMAL<br /> Must be used with --recommend-param |
| *--scale-factor*        | Data scale for test scenarios such as TPCH (unit: GB), defaults to 100<br />Must be used with --recommend-param |
| **Other Parameters** |  |
| *-h,--help*          | View help information for the current command    |
| *--ssl-protocol*        | Specifies the protocol for configuring database [client <-> server trusted channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel), currently only SSL is supported  <br> Must be used with `--ssl-path`                   |
| *--ssl-path*            | Specifies the storage path for SSL-related certificates, must be used with `--ssl-protocol` <br/> When installing the database, all files under this path will be copied to the $YASDB_HOME/ssl path on each server                       |
| *--no-add-yasdba*       | Do not add the installer user to the YASDBA user group <br> **Not recommended**. After specifying this option, the installation user will not be able to log in to the database without password through [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication)          |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy|
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1      |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4            |
| *-o, --output*          | Output path for configuration file   |
| *--dev*                 | For internal development use only  ||
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions/LSFA_LISTAGG) function (corresponding functionality is not applicable to YAC/Distributed Cluster Deployment, no need to specify installation of this plugin separately)<br>* s3: Install plugins for S3 bucket related functionality (corresponding functionality is not applicable to YAC/Distributed Cluster Deployment, no need to specify installation of this plugin separately)<br>* udf: Install plugins for [UDF](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions/XML Function/00XML Function) function |
| *--nvme-reuse*     | Controls whether to check and reuse an existing NVMe-oF disks; reuse is disabled by default. If reuse is not enabled, a warning will be thrown when residual nameof is detected, requiring secondary confirmation from the user. The prerequisites for reuse are: the IP address must be consistent, and the same drive letter name as that passed in during deployment can be found in the corresponding NQN file.                        |
| *--connect-param* | NVMe-oF connection parameters, see nvme connect --help for details |    

<span id="de" name="de"></span>

## package de gen

The command is used to generate a configuration file for ISC Distributed Cluster Deployment based on the input options.


|Option |Meaning |
| --------------------- |-----------------------------------------------------------|
| **Server Configuration Parameters** |  |
| *-u,--username*         | SSH username for the server                  |
| *--groupname*           | User group name, defaults to `username`                  |
| *-p,--password*         | SSH login password          |
| *-N,--no-password*      | SSH passwordless login             |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username<br />It is used to execute commands that require sudo privileges, such as creating cgroup directories |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password      |
| *--host*                | Server connection information expression           |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`        |
| *--port*                | SSH connection port of the server  ||
| **Database Basic Configuration Parameters** |  |
| -c,--cluster       | Name of the generated cluster (required parameter)           |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)         |
| *--data-path*           | Data path of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)     |
| *--log-path*            | Run log path, storing database run.log and slow.log, as well as logs for yasom and yasagent                  |
| *--begin-port*          | Starting port, defaults to 1688|
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)              |
| *--replica-cidr*        | Primary-standby replication link address (defaults to the same IP segment)             ||
| *--din-cidr*            | Distributed network communication link address (defaults to the same IP segment)        |
| *--table-type*          | Main business table type, optional [HEAP,TAC,LSC], defaults to LSC                       |
| *--db_type*           | Type of database, optional [disk,mem], disk is disk database, and mem is memory database. Default is disk.  |
| *--create-cgroup*       | Whether to create the resource management cgroup directory. By default, it is not created. To create it, sudo privileges are required, and you need to specify at least one of the parameter pairs, either -u, -p or -su, -sp     |
| *--cgroup-path*         | Customize the resource management cgroup directory. When not specified, it defaults to `/sys/fs/cgroup` |
| **Node Scale Parameters** |  |
| *-d, --deploy-mode*     | Deployment scale mode for ISC Distributed Cluster Deployment:<br>\* MINI: Minimum scale deployment, deployed 1MN 1CN 3DN on the same server<br>\* NORMAL: Regular deployment, based on the following node scale               |
| *--mn*                  | Scale of MN nodes, defaults to 1           |
| *--cn*                  | Scale of CN nodes, defaults to 1           |
| *--dn*                  | Scale of DN group and nodes, for example, 1-3 means 1 group with 3 nodes (1 primary and 2 standbys within the group)                  |
| **Database Performance Parameters** | If these parameters are not specified during installation, after the installation is completed, they can be adjusted as needed by calling [DBMS_PARAM](../../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_PARAM).OPTIMIZE |
| *--recommend-param*     | Whether to enable recommended parameter functionality                        |
| *--memory-limit*        | The upper limit of the percentage of server memory available for the database to use, defaults to 80. The upper limit of available capacity is calculated as `total server memory * memory-limit`<br />Must be used with --recommend-param |
| *--cpu-limit*           | The upper limit of the percentage of the server's CPU that can be used by the database, defaults to 100. The upper limit of available capacity is calculated as `total server CPU * cpu-limit`<br />Must be used with --recommend-param  ||
| *--scene*             | Scenario for parameter tuning, optional [NORMAL,BATCH,TPCH,AIM], defaults to NORMAL.Must be used with --recommend-param<br>- NORMAL: General mixed workload scenario, applicable to all table types<br>- BATCH: Batch processing scenario, can only be set to BATCH when the table type is HEAP. When the business mainly uses HEAP tables for OLTP and experiences poor performance during periodic batch scenarios, consider using the BATCH scenario for tuning<br />- TPCH: Complex analysis scenario, can only be set to TPCH when the table type is LSC. When using TPCH scenario for tuning recommendation, ensure all nodes execute the parameter recommendation; otherwise, restart may fail due to mismatched connection parameters<br />- AIM: Full memory optimization scenario, can be set to AIM only when deployed as a distributed memory database (i.e., --db_type is specified as mem) |
| *--scale-factor*        | Data scale for test scenarios such as TPCH (unit: GB), defaults to 100<br />Must be used with --recommend-param |
| *--mms_limit*         | In memory database, the upper limit of available memory percentage for caching memory tablespace data, defaults to 80. The upper limit of available capacity is calculated as `physical total memory * memory-limit * mms_limit`, and the remaining part is used for computing and caching non-memory tablespace data, etc.<br />This parameter only takes effect when --db_type is specified as mem and scene is specified as AIM, and it only takes effect on the DN nodes. |
| **Other Parameters** |  |
| *-h,--help*          | View help information for the current command    |
| *--ssl-protocol*        | Specifies the protocol for configuring database [client <-> server trusted channel](../../../Product Security/Encryption/Trusted Channel/00Trusted Channel), currently only SSL is supported  <br> Must be used with `--ssl-path`                   |
| *--ssl-path*            | Specifies the storage path for SSL-related certificates, must be used with `--ssl-protocol` <br/> When installing the database, all files under this path will be copied to the $YASDB_HOME/ssl path on each server                       |
| *--no-add-yasdba*       | Do not add the installer user to the YASDBA user group <br> **Not recommended**. After specifying this option, the installation user will not be able to log in to the database without password through [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication)          |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy|
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1      |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4            |
| *-o, --output*          | Output path for configuration file   |
| *--dev*                 | For internal development use only  ||
| *--node-info*           | Specify the file path of node deployment information, this parameter is used to restore backup sets to an empty cluster, obtained through the *yasrman*'s [View Backup Sets](../../yasrman/User Guide for yasrman/00User Guide for yasrman)     |
| *--plugins*             | Plugins to be installed, separate multiple options with a comma `,`, optional options and their meanings are as follows:<br>* all: Install all plugins, default value<br>* none: Do not install plugins<br>* dblink: Install plugins for DBLink related functionality<br>* gis: Install plugins for built-in [GIS](../../../Development Guide/SQL Reference Manual/Built-in Functions/GIS Function/00GIS Function) function<br>* listagg: Install plugins for built-in [LSFA_LISTAGG](../../../Development Guide/SQL Reference Manual/Built-in Functions/LSFA_LISTAGG) function (corresponding functionality is not applicable to ISC Distributed Cluster Deployment, no need to specify installation of this plugin separately)<br>* s3: Install plugins for S3 bucket related functionality<br>* udf: Install plugins for [UDF](../../../Development Guide/PL Reference Manual/PL Objects/User-Defined Functions) related functionality<br>* xml: Install plugins for built-in [XML](../../../Development Guide/SQL Reference Manual/Built-in Functions/XML Function/00XML Function) function |

## package config join-demo

This command is used to generate a configuration file template `join_demo.toml` for managed command usage.

|Option |Meaning |
| ------------ | ----------------------------------- |
| *-t, --type*   | Type of the database cluster to be managed (required parameter), SE represents Standalone Deployment, DE represents ISC Distributed Cluster Deployment  |
| *-d, --dir*    | Path where the configuration file will be generated (defaults to current directory)  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package config join-demo -t SE
```

## package config show

This command is used to display the generated cluster configuration file information.

|Option |Meaning |
|-----------------| ------------------------------- |
| *-c, --cluster*     | Name of the YashanDB deployed cluster (hidden parameter)         |
| *-t, --toml*        | Name of the cluster configuration file to be displayed (e.g., yashandb.toml) (hidden parameter) |
| *-b, --by*          | Display by server/node group, defaults to host (hidden parameter) |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package config show -c yashandb
```

## package upload

This command is used to update the information in `hosts.toml` and upload a new YashanDB software package.

|Option |Meaning |
| ------------------- | -------------------------- |
| *-t, --toml*          | Path to the target toml file to be updated (required parameter) |
| *-i, --install-pkg*   | Path of the new installation package (deprecated parameter)             |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package upload -t hosts.toml
```

## package install

This command is used to install the software on a remote server (it can install on all or specific servers according to the toml file configuration), the package is in tar.gz format.

|Option |Meaning |
| -------------------- | ------------------------------------ |
| *\-t, --toml*          | Path to the server configuration file `hosts.toml` (required)  |
| *-f, --force*          | Ignore errors and force installation, defaults to false      |
| *--disable*            | Suppress task progress bar display                   |
| *--deps*               | Local path of the dependency package (hidden parameter)               |
| *\-i, --install-pkg*   | Local path of the software package (deprecated parameter)       |
| *--plugin*             | Local path of the plugin package (deprecated parameter)     |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package install -t hosts.toml
```

## package uninstall

This command is used to uninstall the installed software on a remote server (it can uninstall on all or specific servers according to the toml file configuration).

|Option |Meaning |
| ---------------- | ------------------------------------------- |
| *\-c, --cluster*   | Name of the YashanDB deployed cluster (required parameter)            |
| *-t, --toml*       | When uninstalling via SSH, the path to the server configuration file `hosts.toml` needs to be specified |
| *-d, --child*      | Display task and sub-task details                      |
| *-f, --force*      | Force uninstall, default false|
| *--disable*        | Suppress task progress bar display |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
# Uninstall the entire cluster
$ yasboot package uninstall -c yashandb

# Uninstall based on configuration file
$ yasboot package uninstall -c yashandb -t hosts.toml
```

## package upgrade

This command is used to upgrade the installed software (yasom, yasagent) on a remote server.
It must be executed after the database upgrade is completed.

|Option |Meaning |
| ------------- | ---------------------------------------- |
| *\-t, --toml*          | Path to the server configuration file `hosts.toml` (required)  |
| *--package*     | Absolute path of the upgrade package (deprecated parameter)         |
| *--plugin*      | Local path of the plugin package (deprecated parameter)        |
| *-d, --child*   | Display task and sub-task details                   |
| *--disable*     | Suppress task progress bar display                       |
| *--deps*        | Local path of the dependency package (hidden parameter)        |
| *--same-version*| Same version upgrade (hidden parameter)        |
| *--only-replace-without-version-limit*    | Ignore version checks and directly replace binaries, this parameter is a high-risk operation, **do not use in production environments** (hidden parameter)  |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package upgrade -t hosts.toml
```

## package rollback

This command is used to roll back the installed software (yasom, yasagent) on a remote server.

|Option |Meaning |
| ---------------- | ---------------------------------------- |
| *\-c, --cluster*   | Name of the YashanDB deployed cluster (required)         |
| *\-t, --toml*          | Path to the server configuration file `hosts.toml` (required)  |
| *--disable*        | Suppress task progress bar display                       |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package rollback -c yashandb -t hosts.toml
```

## package verify

This command is used to verify whether the installation package has been tampered with.

***Example***

```shell
# After decompressing the installation package, use the *yasboot* in the installation package to execute the following command
$ yasboot package verify
```

## package host

This command is used to modify the configuration information in the server configuration file, currently only the password can be modified.

|Option |Meaning |
|-----------------|-------------------------------------|
| *-t, --toml*        | Path to the target server configuration file to be modified (e.g., hosts.toml) (required parameter) |
| *-k, --key*         | Name of the configuration to be modified (required parameter)                       |
| *-v, --value*       | Value of the configuration to be modified (required parameter)                |
| *--host-id*         | ID of the server where the information needs to be modified (hidden parameter, leave blank to modify all)                    |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ yasboot package host -t hosts.toml -k password -v 123 --host-id host0001
```


## package config gen

This command is used to generate a configuration file based on the input options.

This command is no longer maintained. Please use the package se/de/ce gen command instead.

|Option |Meaning |
| --------------------- | ------------------------------------------- |
| *\-c,--cluster*         | Name of the generated cluster (required parameter)            |
| *-u,--username*         | SSH username for the server|
| *--groupname*           | User group name, defaults to `username`      |
| *-p,--password*         | SSH login password  |
| *-N,--no-password*      | SSH passwordless login  |
| *--ip*                  | IP address of the server's SSH, separate multiple IPs with a comma `,`                      |
| *--port*                | SSH connection port of the server                       |
| *-i, --install-path*    | Database installation path, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)                       |
| *-su,--sudo-username*   | SSH user with sudo privileges, defaults to the same value as --username (used to execute commands that require sudo privileges, such as creating cgroup directories)                   |
| *-sp,--sudo-password*   | Password for the SSH user with sudo privileges, defaults to the same value as --password             |
| *--no-add-yasdba*       | Do not add the installer user to the YASDBA user group          |
| *--boot-start-monit*    | Enable self-starting monit daemon (add startup command to /etc/rc.local)         |
| *--host*                | Server connection information expression                    |
| *-t, --yas-type*        | Deployment shape of the database:<br>\* SE: Standalone Deployment <br>\* CE: YAC/Distributed Cluster Deployment <br>\* DE: ISC Distributed Cluster Deployment, default value |
| *-d, --deploy-mode*     | Deployment scale of ISC Distributed Cluster Deployment:<br>\* MINI: Minimum scale deployment, deployed 1MN 1CN 3DN on the same server<br>\* NORMAL: Regular deployment, based on the following node scale |
| *-L,--local*            | Whether it is a local deployment (no installation package needed), defaults to false, IP defaults to 127.0.0.1                     |
| *--ipv6*                | Whether to use IPv6 for local deployment, defaults to IPv4    |
| *--mn*                  | Scale of MN nodes within ISC Distributed Cluster Deployment, defaults to 1   |
| *--cn*                  | Scale of CN nodes within ISC Distributed Cluster Deployment, defaults to 1   |
| *--dn*                  | Scale of DN group and nodes within ISC Distributed Cluster Deployment, for example, 1-3 means a group with 3 nodes (1 master and 2 standby within the group)|
| *--db*                  | Scale of nodes in standalone DB group, defaults to 1 |
| *--listen-cidr*         | Listen address of the database (defaults to the same IP segment)  |
| *--din-cidr*            | Distributed network communication link address (defaults to the same IP segment)                     |
| *--replica-cidr*        | Primary-standby replication link address (defaults to the same IP segment)  |
| *--begin-port*          | Starting port 1688 |
| *--data-path*           | DATA directory of the database instance, supports numbers, letters (case-sensitive) and some symbols (`/`, `-`, `_`, `.`)                 |
| *-f, --force*           | Whether to force deploy the database, forcing means it will not check whether the current server's running state can deploy               |
| *-o, --output*          | File output path               |
| *--ce*                  | Scale of nodes in YAC type, defaults to 2, maximum 8         |
| *--inter-cidr*          | Internal communication link address of YAC/Distributed Cluster (defaults to the same IP segment)               |
| *--ce-data*             | Data disk in YAC/Distributed Cluster, separate multiple data disk information with a comma `,`                       |
| *--create-cgroup*       | Whether to create the resource management cgroup directory. By default, it is not created. To create it, sudo privileges are required, and you need to specify at least one of the parameter pairs, either -u, -p or -su, -sp     |
| *--cgroup-path*         | Customize the resource management cgroup directory. When not specified, it defaults to `/sys/fs/cgroup` |
| *--recommend-param*     | Whether to enable recommended parameter functionality                   |
| *--memory-limit*        | Maximum percentage limit of memory usable by the server, defaults to 80            |
| *--table-type*          | Make database configuration parameters more suitable for the main business table type, optional [HEAP,TAC,LSC], defaults to HEAP in Standalone Deployment and LSC in ISC Distributed Cluster Deployment          |
| *-fg, --failgroup*      | Number of failure groups in YAC/Distributed Cluster disk group (defaults to 1)          |
| *--ce-group*            | Number of YAC/Distributed Cluster groups, unique parameter in YAC Deployment, default quantity is 1          |
| *--disk-found-path*     | YAC/Distributed Cluster disk discovery path (defaults to /dev/yfs)                      |

Parameter explanations:

- IP: The abbreviation `192.168.157.[1-3]` represents `192.168.157.1, 192.168.157.2, 192.168.157.3` three consecutive IP addresses. If it is an IPv6 address, it needs to be in square brackets, and if it is a link-local address, it also needs to include the network card number. IP can be configured as the following four combinations:
    - IPv4 address
    - IPv6 address
    - IPv4 address with IPv4-mapped IPv6 address (`[::ffff:192.168.157.1]`)
	- IPv6 address with IPv4-mapped IPv6 address
- CIDR: Multiple segments can be configured via `--listen-cidr 0.0.0.0/0,192.168.1.0/22`.
- Password: If SSH passwordless login is configured, the password is optional.

```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip 192.168.157.1,192.168.157.2,192.168.157.3 --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip [fc00:157::129],[fc00:157::130],[fc00:157::131]   --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip [::1],[fe80::20c:29ff:fe75:582b%ens33],[fc00:157::129]  --port 22 --install-path /var/database/yashan --begin-port 1688
```

The above commands are equivalent to:

```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip 192.168.157.[1-3] --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb -u yashan -p password \
--ip  [fc00:157::[129-131]] --port 22 --install-path /var/database/yashan --begin-port 1688
```
```shell
$ yasboot package config gen --cluster yashandb --host yashan:password@[::1],[fe80::20c:29ff:fe75:582b%ens33],[fc00:157::129]:22:/var/database/yashan --begin-port 1688
```

Using server connection information expression:

- Full parameters: `username:password@IP1,IP2,IP3:port:install-path`
- In the case of SSH passwordless configuration, the password can be empty: `username:@IP1,IP2,IP3:port:install-path`
- IP supports abbreviation for consecutive IP addresses: `username:@192.168.157.[1-3]:port:install-path`
- The port can be omitted: `username:password@IP1,IP2,IP3:install-path`
- The installation path can be omitted: `username:password@IP1,IP2,IP3:port`

```shell
$ yasboot package config gen -c yashandb \
--host yashan:password@IP1,IP2,...,IPn:22:/var/database/yashan --begin-port 1688
```
- Disk discovery path: When using a local file system for deployment, to avoid deployment failures, please modify the disk discovery path to a local filesystem path that uses common filesystem formats such as ext4 or xfs.

