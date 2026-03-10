## System Overview

Yashan Cluster Service (hereinafter referred to as YCS) is a cluster management component of YashanDB. It provides capabilities including server management, resource management, cluster monitoring, and cluster high availability, supporting the stable operation of YashanDB YACs from deployment to startup and shutdown.

The following diagram illustrates the physical deployment of the cluster database, which includes two servers and one shared storage hardware. Each server is considered a node in the cluster. YFS runs without an independent process during runtime and is monitored by the YCS process. YCS processes on different servers are interconnected via a network. Database instances have independent processes during runtime, and the instance processes on different servers are interconnected via a network. On the same server, database instances connect to YCS and YFS using UDS.

![](./image/sharedstorage.png)

## Server Management

A YAC consists of several servers. When a physical server fails, YAC can still provide services normally. Each server runs a set of YCS/YFS processes and a set of YashanDB processes.

YCS's management of servers includes adding and removing servers in the cluster, as well as starting and stopping cluster servers.

Server configuration can also be specified during the YashanDB installation process by adjusting the toml file.

YCS provides start and stop commands to control the startup and shutdown of specified servers in the cluster. By default, YCS services, YFS services, and database instances on the servers will be started and stopped together; however, database instances can be configured to start and stop with the YCS service, while YFS cannot be configured for separate start and stop.

## Resource Management

The resources managed by YCS include external resources YashanDB, embedded resources YFS, and node service resources VIP.

For external resources, shell scripts for starting (start.sh) and stopping (stop.sh) resources need to be configured. These shell scripts are automatically generated during the YashanDB installation process and cannot be deleted, as doing so will prevent the database from starting.

Current resource information can be viewed using the `ycsctl show config` command.

## Cluster Monitoring

**Database Status Monitoring**

YCS regularly checks the status of database instances. When an instance is offline, it decides whether to automatically restart the DB based on the configuration parameter AUTO_START and the number of restarts based on the configuration parameter RESTART_TIMES (RESTART_TIMES of 0 indicates no restart for the instance).

**Alarm Events**

YCS implements an alarm mechanism that logs alarm events in the alarm log when specific conditions occur, and clears alarm events when the conditions are resolved (alarms that do not need to be cleared will not be removed).

**Operation Logs**

YCS implements functionality for recording and managing operation logs similar to those of the database, allowing administrators to view and analyze them when needed.

**Operating System Load Monitoring**

YCS implements an operating system load monitoring mechanism called OS Watcher, which regularly collects the current load information of the operating system for administrators to view and analyze when needed.

## Cluster High Availability 

YCS confirms the operational status of each server and resource using network heartbeat and disk heartbeat. When there are anomalies with resources or servers, it will evict the corresponding resources or servers from the cluster to avoid split-brain scenarios and achieve high availability of YAC services.