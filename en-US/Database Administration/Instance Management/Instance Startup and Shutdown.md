## Introduction to Startup and Shutdown Processes and Operating Modes

This document mainly introduces the instances of starting Standalone Deployment and ISC Distributed Cluster Deployment. For information on starting YAC/Distributed Cluster instances, please refer to [YAC/Distributed Cluster Startup and Shutdown](../../Database Administration/Cluster Management/Cluster Startup and Shutdown).

### Three Stages of Database Instance Startup

A database instance must go through three stages: NOMOUNT, MOUNT, and OPEN from being closed to normal use.

*   **NOMOUNT**: Start the database instance, read the parameter file, but do not load the database; only the sys user is allowed to log in.

*   **MOUNT**: Start the database instance, read the control file, load the database, but the database is in a closed state; only the sys user is allowed to log in.

*   **OPEN**: Start the database instance, load and open the database. During the database opening, the [database operating mode](#open_mode) can also be optionally selected as READWRITE, READONLY, or RESETLOGS.

![](./image/stop-start.png)

The running status of the database instance can be checked through the v$instance view.

```sql
$ yasql sys/********@192.168.1.2:1688

SQL> select status from v$instance;
 
STATUS   
---------
MOUNTED 
```

<span id="open_mode" name="open_mode"></span>

### Database Operating Mode



When the database instance moves from the NOMOUNT or MOUNT stage to the OPEN stage, it supports four open modes: READWRITE, READONLY, RESETLOGS, and UPGRADE.

- **READWRITE**: The database defaults to opening in READWRITE mode. In this mode, the database supports full transactional read/write operations and is used in formal production environments.

- **READONLY**: The database is opened in read-only mode, restricting the database to read-only operations without generating any redo. 
  
  - In YAC Deployment, the database cannot be opened in read-only mode.

  - In Standalone Primary/Standby Deployment, the physical standby database opens in this mode by default.

- **UPGRADE**: The upgrade tool *yasboot* opens the database in this mode during the upgrade process. In this mode, new session connections are not allowed, and manual OPEN operations are also not permitted.

- **RESETLOGS**: When a PITR (Point In Time Recovery), database flashback, or logical standby database configuration has occurred, and if a complete recovery cannot be performed, the database must be opened in RESETLOGS mode. This mode will reset the redo log sequence number.



  > **Caution**: 
  >  
  > - In practical use, it is recommended to open the physical standby database in READONLY mode for primary/standby Standalone Deployment; this mode is not applicable for distributed, YAC/Distributed Cluster Deployment.  
  > - If the database is opened in RESETLOGS mode while performing a complete recovery operation, it will generate error YAS-02184 and shut down the instance.

## Preparation for Operations

When starting the database instance, ensure that the yasom and yasagent processes are running; otherwise, the startup and shutdown tool *yasboot* will prompt a *connect: connection refused* error. Please confirm that the database service processes are running normally with the command `ps -ef | grep -E "yasom|yasagent|yasdb"`.

When the server is rebooted, the yasom and yasagent processes will automatically stop. By default, the database service will not automatically restart. Refer to the following operations to restart the database service with the database installation user or register the autostart functionality to automatically start the yasom and yasagent processes when the server is rebooted. For specific operations, refer to [Configure Autostart](#auto_start).

```shell
# Manually start yasom and yasagent processes
$ yasboot process yasom start -c yashandb -t /home/yashan/install/hosts.toml
$ yasboot process yasagent start -c yashandb -t /home/yashan/install/hosts.toml

# The database can only be started after the yasom and yasagent processes are started
$ yasboot cluster start -c yashandb

# Start daemon processes as needed
$ yasboot monit start --cluster yashandb
```

## Starting Database Instance

- yasboot tool: YashanDB supports adjusting the database instance to any stage using the *yasboot* tool.
- SQL statement: YashanDB supports adjusting the database instance from the NOMOUNT stage to the MOUNT stage or OPEN stage using the ALTER DATABASE statement, but the ALTER DATABASE statement only affects the executing instance.

### Start to NOMOUNT Stage

Use the *yasboot* tool as the database installation user to start the database instance to the NOMOUNT stage, with the following commands:

```shell
# Method 1: First stop then start the database cluster to the NOMOUNT stage
$ yasboot cluster stop -c yashandb
$ yasboot cluster start -c yashandb -m nomount
# Method 2: One-click restart the database cluster to the NOMOUNT stage
$ yasboot cluster restart -c yashandb -m nomount

# Start the node group with group_id 1 to the NOMOUNT stage
$ yasboot group start -c yashandb -g 1 -m nomount
# Start the node with node_id 4-1 to the NOMOUNT stage
$ yasboot node start -c yashandb -n 4-1 -m nomount
```

After the database instance starts to the NOMOUNT stage, check the STATUS field value in the V$INSTANCE view which should be STARTED.

```sql
$ yasql sys/********@192.168.1.2:1688

SQL> select status from v$instance;
 
STATUS   
---------
STARTED 
```

### Start to MOUNT Stage

- yasboot tool: The database installation user can adjust the database instance from any stage to the MOUNT stage.
- SQL statement: Connect to the database with the sys user and use the ALTER DATABASE statement to adjust the database instance from the NOMOUNT stage to the MOUNT stage.

::: tabs

== yasboot tool

```shell
# Method 1: First stop then start the database cluster to the MOUNT stage
$ yasboot cluster stop -c yashandb
$ yasboot cluster start -c yashandb -m mount
# Method 2: One-click restart the database cluster to the MOUNT stage
$ yasboot cluster restart -c yashandb -m mount

# Start the node group with group_id 1 to the MOUNT stage
$ yasboot group start -c yashandb -g 1 -m mount
# Start the node with node_id 4-1 to the MOUNT stage
$ yasboot node start -c yashandb -n 4-1 -m mount
```

== SQL statement

```shell
# At this point, ensure the database instance is in the NOMOUNT stage
$ yasql sys/********@192.168.1.2:1688

SQL> ALTER DATABASE MOUNT;
```

:::

After the database instance starts to the MOUNTED stage, connect to the database with any user to check that the STATUS field value in the V$INSTANCE view has been updated to MOUNTED.

```shell
$ yasql sys/********@192.168.1.2:1688

SQL> select status from v$instance;
 
STATUS   
---------
MOUNTED 
```

### Start to OPEN Stage

The database can be started to the OPEN stage in the following two ways:

- yasboot tool: The database installation user can adjust the database instance from any stage to the OPEN stage.
- SQL statement: Connect to the database with the sys user and use the ALTER DATABASE statement to adjust the database instance from the NOMOUNT or MOUNT stage to the OPEN stage.

::: tabs

== yasboot tool

```shell
# yasboot tool:
# Method 1: First stop then start the database cluster to the OPEN stage
$ yasboot cluster stop -c yashandb
$ yasboot cluster start -c yashandb -m open
# Method 2: One-click restart the database cluster to the OPEN stage
$ yasboot cluster restart -c yashandb -m open

# Start the node group with group_id 1 to the OPEN stage
$ yasboot group start -c yashandb -g 1 -m open
# Start the node with node_id 4-1 to the OPEN stage
$ yasboot node start -c yashandb -n 4-1 -m open
```

== SQL statement

```shell
# Here, an example is shown with the user sys to start the database from the MOUNT stage to the OPEN stage:
$ yasql sys/********@192.168.1.2:1688

-- Open the database in READWRITE mode by default
SQL> ALTER DATABASE OPEN;

-- Open the standby database in READONLY mode 
SQL> ALTER DATABASE OPEN READONLY;
```
:::

After the database instance starts to the OPEN stage, check that the STATUS field value in the V$INSTANCE view has been updated to OPEN.

```shell
$ yasql sales/********@192.168.1.2:1688

SQL> select status from v$instance;
 
STATUS   
---------
OPEN 
```

The operating mode of the database can be checked through the V$DATABASE view.

```sql
SQL> SELECT database_id,database_name,open_mode FROM V$DATABASE;
 
    DATABASE_ID DATABASE_NAME    OPEN_MODE       
--------------- ---------------- -----------------
      569377301 yasdb            READ_WRITE   
```

When deploying primary/standby, pay attention to the DATABASE_ROLE field indicating the primary/standby role.

```sql
SQL> SELECT DATABASE_NAME,LOG_MODE,OPEN_MODE,PROTECTION_MODE,DATABASE_ROLE,BLOCK_SIZE,STATUS FROM  V$DATABASE;
  
DATABASE_NAME LOG_MODE    OPEN_MODE   PROTECTION_MODE      DATABASE_ROLE  BLOCK_SIZE STATUS
------------- ----------- ----------- -------------------- ------------- ----------- -------
yasdb         ARCHIVELOG  READ_ONLY   MAXIMUM PERFORMANCE  STANDBY              8192 NORMAL
```

## Closing Database Instance

- yasboot tool: YashanDB supports closing the database instance through the *yasboot* tool.
- SQL statement: YashanDB supports closing the database instance using the SHUTDOWN statement, but the SHUTDOWN statement only affects the executing instance.

This document mainly introduces the closing operations of Standalone Deployment and ISC Distributed Cluster Deployment instances. For information about closing YAC/Distributed Cluster instances, refer to [YAC/Distributed Cluster Startup and Shutdown](../../Database Administration/Cluster Management/Cluster Startup and Shutdown).

### Closing Modes

YashanDB supports closing the database instance in the following three modes:

*   NORMAL: Wait for the completion of transactions before closing the database, with no waiting time limit. It is recommended to choose this mode to close the database.

*   IMMEDIATE: Forcefully interrupt all database operations, roll back uncompleted transactions, and close the database after waiting for dirty pages to be flushed.

*   ABORT: Forcefully interrupt all database operations and close the database without waiting for dirty pages to be flushed. This closing mode will increase the startup time as it does not wait for dirty pages to be flushed.

  > **Caution**: 
  >
  > It is only recommended to use the ABORT mode when the server crashes, loses power, or forcefully shuts down the database; otherwise, this mode should be avoided.

### Closing Operations

Both the *yasboot* tool and SQL statement can close the database instance in the specified mode, with commands/statements as follows:

::: tabs

== yasboot tool

```shell
# You can specify the closing mode using the -s parameter; if omitted, it defaults to IMMEDIATE
$ yasboot cluster stop -c yashandb
$ yasboot cluster stop -c yashandb -s normal
$ yasboot cluster stop -c yashandb -s immediate
$ yasboot cluster stop -c yashandb -s abort

# Close the node group with group_id 1
$ yasboot group stop -c yashandb -g 1
# Close the node with node_id 4-1
$ yasboot node stop -c yashandb -n 4-1 -s normal
```

== SQL statement

```shell
# Use keywords to specify closing modes; if omitted, it defaults to NORMAL
$ yasql sales/********@192.168.1.2:1688

SQL> SHUTDOWN NORMAL;
SQL> SHUTDOWN IMMEDIATE;
SQL> SHUTDOWN ABORT;
```
:::

<span id="auto_start" name="auto_start"></span>

## Configure Autostart on Boot

When the database server is rebooted due to external factors, by default the database service will not automatically restart. You can configure the database service to start automatically on boot as needed.



1. Create and edit the service startup script.

    The content of the script is for example only, and the path, variable values, and other information must be modified according to the actual situation.

    ```shell
    $ sudo vi /usr/local/bin/yashan_monit.sh
    ```



   ```bash
   #!/bin/bash
   MONIT_AUTOSTART="true"                  # Monitor the monit process; true means yes, other values mean no
   YASDB_USER=yashan                               # YashanDB installation user
   YASDB_HOME=/data/yashan/yasdb_home/{version_number}   # YashanDB HOME directory
   INTERVAL=3                                # Monitoring interval in seconds; periodically checks the corresponding process by name; if not running, restart
   
   # Load YashanDB environment variables
   env LD_LIBRARY_PATH=$YASDB_HOME/lib
   
   while true; do    
       if "$MONIT_AUTOSTART" = "true"; then
           if ! pgrep -a monit | grep "$YASDB_HOME" > /dev/null; then
               echo "$(date) monit abnormal, try restart..." 
               su - $YASDB_USER -c "source ~/.bashrc && $YASDB_HOME/om/bin/monit -c $YASDB_HOME/om/monit/monitrc" &
           fi
       fi
       sleep "$INTERVAL"
   done
   ```



2. Grant the script executable privilege.

    ```shell
    $ sudo chmod +x /usr/local/bin/yashan_monit.sh
    ```



3. Create and write the service unit configuration file.

   ```shell
   $ sudo vi /etc/systemd/system/yashan_monit.service
   
   [Unit]
   Description=Yashan Monitor
   After=network.target
   
   [Service]
   ExecStart=/usr/local/bin/yashan_monit.sh
   Type=simple
   Restart=always
   StandardOutput=syslog
   StandardError=syslog
   
   [Install]
   WantedBy=multi-user.target
   
   ```



1. Activate and start the service.

    ```shell
    $ sudo systemctl daemon-reload

    $ sudo systemctl enable yashan_monit
    Created symlink from /etc/systemd/system/multi-user.target.wants/yashan_monit.service to /etc/systemd/system/yashan_monit.service.

    $ sudo systemctl start yashan_monit
    ```





2. Check the service status.

    ```shell
    $ systemctl status yashan_monit
    yashan_monit.service - Yashan Monitor
    Loaded: loaded (/etc/systemd/system/yashan_monit.service; enabled; vendor preset: disabled)
    Active: active (running) since Thu 2025-06-12 10:41:01 CST; 4s ago
    Main PID: 3580 (yashan_monit.sh)
    CGroup: /system.slice/yashan_monit.service
           ├─3580 /bin/bash /usr/local/bin/yashan_monit.sh
           └─3589 sleep 3
    ```



6. (Optional) If you no longer need to enable autostart on boot, execute the following commands to disable the service.

   ```shell
   $ sudo systemctl stop yashan_monit
   
   $ sudo systemctl disable yashan_monit
   Removed symlink /etc/systemd/system/multi-user.target.wants/yashan_monit.service.
   
   $ systemctl status yashan_monit
   ● yashan_monit.service - Yashan Monitor
       Loaded: loaded (/etc/systemd/system/yashan_monit.service; disabled; vendor preset: disabled)
       Active: inactive (dead)
   ```
