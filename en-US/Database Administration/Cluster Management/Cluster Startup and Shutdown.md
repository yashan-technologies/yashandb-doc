The startup and shutdown of YAC/Distributed Cluster supports the following two methods:

- Using relevant commands of the [*ycsctl*](../../Tools Guide/ycsctl/00ycsctl) to start or stop instances on a specific server. This requires the environment variable $YASCS_HOME (i.e., the YCS HOME directory) to be correctly configured on the target server. This directory contains shell scripts for managing external resources.

- Using relevant commands of the [*yasboot*](../../Tools Guide/yasboot/Introduction to yasboot Command/00Introduction to yasboot Command) to start or stop the entire cluster or a specific node (operating both YCS services and database instances simultaneously).

## Process System

A YAC/Distributed Cluster server needs to run cluster service processes and database processes to provide cluster database services. The cluster service processes include yascsm (YCSM), yascs (YCS), and *ycsrootagent* (YCSRA).

- yascsm process: Responsible for the normal startup and shutdown of the yascs process and monitoring whether the yascs process is abnormal, providing high availability capabilities for restarting yascs.
- yascs process: Responsible for building and maintaining consistent member relationships in the cluster, starting and stopping the YasFS file system and database services, and providing fault arbitration capabilities when cluster member servers are abnormal.
- ycsrootagent process: A privileged process that requires root privilege to start, providing the capability to perform high privilege operations such as executing I/O Fencing.

### Startup and Shutdown Status

In YCS, "online" indicates that it is in an active running state, while "offline" indicates a stopped state, represented in the topology diagram as follows:

```shell
Host ID   |Target    |State     |YasFS     |YasDB
----------------------------------------------------------------
1          online     online     online     online 
```

YCS controls the management of the operation of the entire YAC/Distributed Cluster. The startup and shutdown status is displayed according to the resources it manages, including:

- The State field indicates the current status of the YCS service.
- The YasFS field indicates the current status of the YFS service.
- The YasDB field indicates the current status of the database instance.
- The Target field indicates whether the database instance is started together with YCS, depending on the parameter AUTO_START. When AUTO_START=ALWAYS, Target is online; when AUTO_START=NEVER, Target is offline.

### Startup and Shutdown Sequence

By default, the startup and shutdown process of the cluster server includes the startup and shutdown of YCSM, YCS, YFS, and the database instance. Among them, YCSM is the dedicated monitoring process for YCS, YFS is an embedded resource of YCS, and the database instance is an external resource of YCS. The sequence and relationships of these services during the startup and shutdown process are as follows:

- After starting YCSM, it starts and monitors YCS.
- Starting YCS will automatically start the YFS service, and stopping YCS will also automatically stop the YFS service.
- The database instance startup depends on the file system managed by YFS and the topology information managed by YCS, so the database instance is started by YCS after the successful startup of YCS/YFS services.
- When stopping YCS, the database instance will be stopped first, then the YCS/YFS services will be stopped, and finally, YCSM will exit.

YCSRA does not participate in the above startup and shutdown process. When the cluster is configured with services such as I/O Fencing, which must interact with the operating system and hardware devices under root privilege, the user needs to manually start YCSRA using sudo during cluster deployment and server boot; if the YCSRA process is abnormal, the user also needs to manually restart it using sudo.

## Database Instance Startup and Shutdown

The database instance startup is controlled by the AUTO_START parameter, which determines whether the database instance starts together with YCS. When the parameter is set to ALWAYS, the database instance starts together with YCS; when the parameter is set to NEVER, the database instance does not start with YCS and can be started separately by external commands later.

Different command options control whether the database instance stops together with YCS. When the option is `ycs`, the database instance stops together with YCS; when the option is `instance`, it will stop only the database instance.

### Starting YCS

The following command can be used to start YCS. After the system receives the command, it will first start YCSM, then YCS, and simultaneously start the YFS service thread. Then it will determine whether to start the database instance based on the value of the AUTO_START parameter:

```shell
# Start ycs on server 0
$ ycsctl start ycs

$ ycsctl status
# AUTO_START=ALWAYS
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               1               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          online     online     online     online     
2          offline    offline    offline    offline    
# AUTO_START=NEVER
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               255               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          offline    online     online     offline     
2          offline    offline    offline    offline    
```

In addition, when AUTO_START is configured to ALWAYS (i.e., simultaneously starting the database instance on this server), the -startoption parameter can be used to specify the database instance's startup mode.

```shell
# Start ycs on server 0
$ ycsctl start ycs -startoption mount

$ ycsctl status
# AUTO_START=ALWAYS
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               1               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          online     online     online     online     
2          offline    offline    offline    offline    
# Log in to database locally to check instance status
$ yasql sys/********

SQL> SELECT status FROM v$instance;
STATUS            
----------------- 
MOUNTED          

# AUTO_START=NEVER
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               255               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          offline    online     online     offline     
2          offline    offline    offline    offline    
```

### Starting Database Instance

The command `ycsctl start instance [-startoption start_option]` can be used to start the database instance on the server individually. The -startoption parameter can be used to specify the database instance startup status; if not specified, it defaults to starting in OPEN status.

* Example of starting without specifying a mode:

```shell
$ ycsctl start instance
$ ycsctl status
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               1               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          online     online     online     online     
2          offline    offline    offline    offline    

# Log in to database locally to check instance status
$ yasql sys/********

SQL> SELECT status FROM v$instance;
STATUS            
----------------- 
OPEN             
```

* Example of starting with specified mode:

```shell
$ ycsctl start instance -startoption mount
$ ycsctl status
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               1               1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          online     online     online     online     
2          offline    offline    offline    offline    

# Log in to database locally to check instance status
$ yasql sys/********

SQL> SELECT status FROM v$instance;
STATUS            
----------------- 
MOUNTED          
```

> **Note**:
>
> When the YCS service on the server is not started, attempting to start the database instance separately will result in a Connection refused error due to the inability to establish a UDS connection with YCS.
>
> It is recommended to wait until all instances in the cluster are successfully started (in OPEN status) before beginning to use the database.

### Stopping Database Instance

The command `ycsctl stop instance` can be used to stop the database instance on the server individually. Please note that the shutdown operation performed at this time is shutdown immediate.

```shell
$ ycsctl stop instance

$ ycsctl status
-----------------------------------------------------------------------------------------
Self Host ID|Cluster Master ID|YasFS Master ID|YasDB Master ID|Active Host Count
-----------------------------------------------------------------------------------------
1            1                 1               255             1         
-----------------------------------------------------------------------------------------
Host ID   |Target    |State     |YasFS     |YasDB
-----------------------------------------------------------------------------------------
1          offline    online     online     offline   
2          offline    offline    offline    offline   
```

### Forcefully Stopping Database Instance

Forceful stopping means sending an immediate terminate process signal to the database instance, causing it to exit immediately regardless of its current state.

The parameter WAIT_STOP_FIN_TIME controls how long to wait after executing the stop script before using a forced stop.

0 means wait indefinitely without using forced stop; other values indicate waiting for that number of seconds before executing a forced stop if the database has not stopped.

### Stopping YCS

The following command stops the YCS service and simultaneously stops the YFS service. Before that, use shutdown immediate to close any running database instances and finally stop the YCSM monitoring process:

```shell
$ ycsctl stop ycs
```

> **Note**: 
>
> - In scenarios where YashanDB resources exist, YCS must wait for the database instance to exit normally before it exits.
>
> - Failure scenarios when stopping YCS: Database instances close slowly.
>
> This kind of problem may occur because there are a large number of concurrent operations in the database during the stop server process, causing the database to take time to handle normal stops before the shutdown completes.

## Cluster Startup and Shutdown

The *yasboot* tool can be used to start and stop the entire YAC/Distributed Cluster, which is equivalent to performing the "Start YCS" and "Stop YCS" operations on each server in the cluster.

It is not possible to specify starting individual database instances (i.e., adjusting the AUTO_START parameter) during cluster startup and shutdown, as this will lead to a startup error.

When using the *yasboot* tool to start or restart YAC/Distributed Cluster, starting in mount status is currently not supported.

```shell
# Stop YAC/Distributed Cluster
$ yasboot cluster stop -c yashandb
# Start YAC/Distributed Cluster
$ yasboot cluster start -c yashandb
# Restart YAC/Distributed Cluster
$ yasboot cluster restart -c yashandb
```
