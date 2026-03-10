This chapter will introduce how to configure resource management in non-CDBs or PDB local resource management in CDBs.

## Environment Preparation

Before using CPU resource management, Cgroups must be configured.

Cgroups (Control Groups) is a mechanism provided by the Linux kernel to group a series of tasks and their subprocesses into different groups, limiting, recording, and isolating resource usage for various groups.

### Scenario 1: YashanDB is not installed

1. In the [YashanDB Standalone Deployment ](../../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/Standalone (Primary-Standby) Deployment) or [YashanDB ISC Distributed Cluster Deployment ](../../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/ISC Distributed Cluster Deployment) at `Step1: Generate Deployment File`, add the `--create-cgroup` option to create the cgroup directory.

2. Execute the following command to generate the deployment file with CPU resource management functionality.

```shell
# Standalone
$ yasboot package se gen --cluster yashandb -u yashan -p password --ip 192.168.1.2 --port 22 \
--install-path /data/yashan/yasdb_home  --data-path /data/yashan/yasdb_data --begin-port 1688 \
--create-cgroup --sudo-username yashan --sudo-password password

# Distributed
$ yasboot package de gen --cluster yashandb -u yashan -p password --ip 192.168.1.2 --port 22 \
--install-path /data/yashan/yasdb_home  --data-path /data/yashan/yasdb_data --begin-port 1688 \
--create-cgroup --sudo-username yashan --sudo-password password
```

After deployment is complete, a command will be automatically added to the /etc/rc.local file to enable CPU resource management functionality after the database starts.

### Scenario 2: YashanDB is already installed

Create the cgroup directory using the `host cgroup create` command of the *yasboot* tool. Refer to [yasboot host](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot host) for detailed parameter descriptions.

```shell
$ yasboot host cgroup create --cluster yashandb --sudo-username yashan --sudo-password ssh password
```

The command needs to be followed by a database restart to enable CPU resource management functionality.

## Enable Resource Management

### Create Resource Consumer Groups

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_CONSUMER_GROUP to create a resource consumer group.

```plsql
-- Create resource consumer groups named LOW_GROUP and HIGH_GROUP, executed only by SYS user with privilege
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP('LOW_GROUP','CONSUMER GROUP WITH LOW RESOURCE');
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP('HIGH_GROUP','CONSUMER GROUP WITH HIGH RESOURCE');
```

### Create Resource Plan

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_PLAN to create a resource plan.

```plsql
-- Create a resource plan named RESPLAN, executed only by SYS user with privilege
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN('RESPLAN','PLAN FOR RESOURCE TEST');
```

### Create Resource Plan Directive

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_PLAN_DIRECTIVE to create a resource plan directive.

```plsql
-- Create a plan directive for LOW_GROUP resource consumer group under RESPLAN resource plan
-- This directive specifies a CPU share of 50, a maximum utilization limit of 10%, 
-- a user memory usage limit of 20%, and a session usage limit of 5%
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN', 'LOW_GROUP', SHARES => 50, UTILIZATION_LIMIT => 10, SPA_LIMIT => 20, SESSION_SPA_LIMIT => 5);

-- Create a plan directive for HIGH_GROUP resource consumer group under RESPLAN resource plan
-- This directive specifies a CPU share of 100, a maximum utilization limit of 100%, 
-- a user memory usage limit of 50%, a session usage limit of 10%, 
-- and reserves 50% of user memory for each session in the resource consumer group
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN', 'HIGH_GROUP', SHARES => 100, UTILIZATION_LIMIT => 100, SPA_LIMIT => 50, SESSION_SPA_LIMIT => 10, SPA_LIMIT_RESERVED => 50);
```

### Create User Mapping

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).SET_CONSUMER_GROUP_MAPPING to create user mapping.

If the corresponding user does not exist, please create the user first. Refer to [Create User](../../Product Security/Identity Identification and Authentication/Managing Users) for specific operations.

```plsql
-- Map user RESMAN1 to LOW_GROUP resource consumer group and user RESMAN2 to HIGH_GROUP resource consumer group, 
-- executed only by SYS user with privilege
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING('USER','RESMAN1','LOW_GROUP');
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING('USER','RESMAN2','HIGH_GROUP');
```

### Activate and Enable Resource Management

1. Modify the `RESOURCE_MANAGER_PLAN` parameter using the *yasboot* tool to activate the resource plan RESPLAN.

```shell
# -r parameter indicates rollback of cluster parameters on modification failure
$ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v RESPLAN -d -r

# Check if the RESOURCE_MANAGER_PLAN parameter has taken effect on all nodes
$ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
```

> **Note**:
>
> The `RESOURCE_MANAGER_PLAN` parameter can only be modified using the `yasboot cluster config set` command.

2. Connect to the database and check if the resource plan is successfully activated. A VALUE of RESPLAN for the `RESOURCE_MANAGER_PLAN` parameter indicates successful activation.

```sql
show parameter RESOURCE_MANAGER_PLAN;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
RESOURCE_MANAGER_PLAN                                            RESPLAN
```

3. Modify the `RSRC_MODE` parameter using the *yasboot* tool to enable memory (MEM) or CPU resource management functionality, then restart the database to apply changes.

```shell
# Change RSRC_MODE parameter to ALL, indicating that both CPU and memory resource management functionality are enabled
$ yasboot cluster config set -c yashandb -k RSRC_MODE -v ALL -d
$ yasboot cluster restart -c yashandb
# Check the result of RSRC_MODE parameter modifications across all nodes
$ yasboot cluster config show -c yashandb -q RSRC_MODE
```

> **Note**:
>
> The `RSRC_MODE` parameter can only be modified using the `yasboot cluster config set` command.

4. After the restart, connect to the database and check if the resource management functionality is successfully enabled. A VALUE of ALL for the `RSRC_MODE` parameter indicates successful enabling.

```sql
show parameter RSRC_MODE;

NAME                                                             VALUE
---------------------------------------------------------------- ----------------------------------------------------------------
RSRC_MODE                                                        ALL
```

After successfully enabling resource management functionality with the above example, when the user RESMAN1 connects to the database, it will be constrained by the directives of the LOW_GROUP resource consumer group under the resource plan RESPLAN.

## Modify Mapping Relationships

If you need to change the resource consumer group mapped to a specific user, ensure that the new resource consumer group already exists. The following statement is just an example; refer to [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER) for specific parameters.

```plsql
-- Map RESMAN1 to the HIGH_GROUP
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING('USER','RESMAN1','HIGH_GROUP');
```

After modifying the mapping relationship, any new session created by the user will belong to the new resource consumer group, while existing sessions will continue to belong to their original consumer group until they end.

## Monitor Resource Usage

### Monitor CPU Resource Usage

YashanDB provides dynamic views [V$CPUSTAT](../../Reference Manual/System Views/Dynamic Performance Views/V$CPUSTAT) and [GV$CPUSTAT](../../Reference Manual/System Views/Dynamic Performance Views/GV$CPUSTAT) to monitor the CPU usage of resource consumer groups across the cluster.

```sql
-- Monitor CPU usage of LOW_GROUP resource consumer group on all nodes
SELECT RESNAME, NR_THROTTLED, THROTTLED_TIME FROM GV$CPUSTAT WHERE RESNAME = 'LOW_GROUP';
```

### Monitor SPA Memory Resource Usage

YashanDB provides dynamic views to monitor the SPA memory usage of resource consumer groups and sessions.

#### Monitor SPA Memory Usage of Resource Consumer Groups

YashanDB provides dynamic views [V$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_CONSUMER_GROUP) and [GV$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_CONSUMER_GROUP) to monitor the SPA memory usage of resource consumer groups across the cluster.

```sql
-- Monitor SPA memory usage of HIGH_GROUP resource consumer group on all nodes
SELECT SPA_LIMIT_QUOTA, SESSION_SPA_LIMIT_QUOTA, SPA_REMAIN_QUOTA, SPA_MAX_USE_QUOTA, SESSION_SPA_RESERVED_QUOTA, SPA_LIMIT_EXCEED_TIMES, 
SESSION_SPA_LIMIT_EXCEED_TIMES FROM GV$RSRC_CONSUMER_GROUP WHERE NAME = 'HIGH_GROUP';
```

#### Monitor SPA Memory Usage of Sessions

YashanDB provides dynamic views [V$SESSION_SPA](../../Reference Manual/System Views/Dynamic Performance Views/V$SESSION_SPA) and [GV$SESSION_SPA](../../Reference Manual/System Views/Dynamic Performance Views/GV$SESSION_SPA) to monitor the SPA memory usage of sessions across the cluster.

```sql
-- Monitor SPA memory usage of the current session
SELECT B.*, A.SPA_USE_MEM, A.SPA_USE_QUOTA, A.RESOURCE_CONSUMER_GROUP 
FROM V$SESSION A, V$SESSION_SPA B WHERE A.SID = B.SID AND B.SID = USERENV('SID');
```

#### Monitor Execution Retries

In an ISC Distributed Cluster Deployment scenario, execution retry conditions can be monitored using the following statement.

```sql
-- Monitor the execution retry conditions of statements in the distributed cluster
SELECT GROUP_ID,GROUP_NODE_ID,SID,GLOBAL_SESSION_ID,SERIAL#,SPA_USE_MEM,SPA_USE_QUOTA,SPA_MAX_USE_MEM,RESOURCE_CONSUMER_GROUP,
EXEC_START_TIME,RETRY_CNT,RETRY_INFO,EXEC_STATUS FROM GV$SESSION WHERE TYPE='USER';
```

### Monitor Parallel Execution Resource Usage

YashanDB provides dynamic views [V$PX_RES_MGR](../../Reference Manual/System Views/Dynamic Performance Views/V$PX_RES_MGR) and [GV$PX_RES_MGR](../../Reference Manual/System Views/Dynamic Performance Views/GV$PX_RES_MGR) to monitor parallel execution resource usage of resource consumer groups across the cluster.

```sql
-- Monitor parallel execution resource usage of HIGH_GROUP resource consumer group on all nodes
SELECT GROUP_ID, GROUP_NODE_ID, PX_RES_TYPE, MAX_PX_RES_USAGE, CURR_PX_RES_USAGE FROM GV$PX_RES_MGR WHERE CONSUMER_GROUP = 'HIGH_GROUP';
```

### Monitor Execution Scheduling within Resource Consumer Groups

YashanDB provides dynamic views [V$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_CONSUMER_GROUP) and [GV$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_CONSUMER_GROUP) to monitor execution scheduling conditions of resource consumer groups across the cluster.

```sql
-- Monitor execution scheduling conditions of HIGH_GROUP resource consumer group on all nodes
SELECT CONCURRENCY_LIMIT, CONCURRENCY_NUM, EXECUTION_WAITERS, REQUESTS, QUEUE_NUMBER, CONCURRENCY_LIMIT_HIT, 
QUEUED_TIME, QUEUE_TIMEOUTS, PARALLEL_DOWNGRADE_TIMES FROM GV$RSRC_CONSUMER_GROUP WHERE NAME = 'HIGH_GROUP';
```

### Monitor Execution Scheduling within Sessions

YashanDB provides dynamic views [V$RSRC_SESSION_INFO](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_SESSION_INFO) and [GV$RSRC_SESSION_INFO](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_SESSION_INFO) to monitor execution scheduling conditions of sessions across the cluster.

```sql
-- Monitor execution scheduling conditions of all sessions under HIGH_GROUP resource consumer group on all nodes
SELECT QUEUE_ID, CURRENT_QUEUED_TIME, QUEUE_TIMEOUTS, PARALLEL_DOWNGRADE_TIMES FROM GV$RSRC_SESSION_INFO WHERE CURRENT_CONSUMER_GROUP = 'HIGH_GROUP';
```

## Disable Resource Management

To disable resource management, follow these steps:

- To delete resource consumer groups, ensure that the relevant mapping relationships and resource plan directives are deleted first, or an error will be returned.
- To delete a resource plan, ensure that the resource plan is not active and delete all resource plan directives associated with the resource plan first, or an error will be returned.

### Disable Resource Management

1. Use the *yasboot* tool to set the `RESOURCE_MANAGER_PLAN` parameter to disable all resource plans.

```shell
$ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v '' -d -r
# Check the results of the RSRC_MODE parameter modification across all nodes
$ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
```

2. Use the *yasboot* tool to set the `RSRC_MODE` parameter to disable resource management functionality and restart the database for the changes to take effect.

```shell
$ yasboot cluster config set -c yashandb -k RSRC_MODE -v NONE -d
$ yasboot cluster restart -c yashandb
# Check the results of the RSRC_MODE parameter modification across all nodes
$ yasboot cluster config show -c yashandb -q RSRC_MODE
```

### Delete User Mapping

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_CONSUMER_GROUP_MAPPING to delete user mapping relationships.

>**Note**:
>
>- When [deleting users](../../Product Security/Identity Identification and Authentication/Managing Users), the user's mapping relationships will be deleted.
>- When deleting a specific user's mapping relationship, the user will be automatically mapped to the DEFAULT_CONSUMER_GROUP.
>- The mapping relationship of the system user SYS cannot be deleted.

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING('USER','RESMAN1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING('USER','RESMAN2');
```

### Delete Resource Plan Directives

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_PLAN_DIRECTIVE to delete resource plan directives.

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE('RESPLAN','LOW_GROUP');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE('RESPLAN','HIGH_GROUP');
```

### Delete Resource Plan

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_PLAN to delete a resource plan.

If the resource plan still has other directives, an error will be returned.

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN('RESPLAN');
```

### Delete Resource Consumer Groups

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_CONSUMER_GROUP to delete resource consumer groups.

If the resource consumer group still has other consumers, an error will be returned.

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP('LOW_GROUP');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP('HIGH_GROUP');
```
