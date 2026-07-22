This chapter will introduce how to configure resource management in non-CDBs or PDB local resource management in CDBs.

## Environment Preparation

Before using **CPU** resource management, it is necessary to configure Control Groups (CGroups) first.

### Step1: Confirm that CGroups are enabled on the server

CGroups is a resource-limiting mechanism provided by the Linux kernel. Most mainstream Linux distributions (e.g., CentOS/RHEL 7+) enable this feature by default. You can follow the steps below to further confirm whether it is enabled:



1. Log in to the database installation server using the installation user.


2. Execute the following command to confirm whether CGroups are enabled.

    ```shell
    $ mount | grep -E "cgroup|cgroup2"
    # or
    $ sudo mount | grep -E "cgroup|cgroup2"
    ```

    If this command returns no output, it indicates that CGroups are not enabled. At this point, you must first [enable CGroups](../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Configuring the Installation Environment.md#CGroups) for each server before proceeding with subsequent operations.

3. Execute the following command to confirm whether CPU and IO are available.

    ```shell
    $ cat /sys/fs/cgroup/cgroup.subtree_control
    ```

    - If the output contains `cpu` and `io`, the functions are available and no further action is required.

    - If the output does not contain `cpu` and `io`, execute the following commands to enable them.

        ```shell
        $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
        $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
        ```



### Step 2: Create a dedicated cgroup directory for YashanDB

#### Scenario 1: YashanDB is not installed

In the **Step 1: Generate Configuration File** of [YashanDB Server Installation](../../Installation and Upgrade/Installation and Deployment/YashanDB Installation via CLI/00YashanDB Installation via CLI), add the `--create-cgroup` option to create the cgroup directory.

#### Scenario 2: YashanDB is already installed

1. Log in to the database installation server using the installation user.


2. Create the cgroup directory through the [host cgroup create](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot host.md#cgroup) command of the *yasboot* tool.

    ```shell
    $ yasboot host cgroup create --cluster yashandb --sudo-username yashan --sudo-password sudopassword
    ```

    >**Note**:
    >
    > In scenarios where YashanDB has been installed, the database should be restarted after creating the cgroup directory. However, to simplify the operation process, it can be restarted when performing the subsequent [Activate and Enable CPU Resource Management](#Activate) operation.

## Enable Resource Management

### Step1: Create Resource Consumer Groups

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_CONSUMER_GROUP to create a resource consumer group.

    ```plsql
    -- Create resource consumer groups named LOW_GROUP and HIGH_GROUP
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP('LOW_GROUP','CONSUMER GROUP WITH LOW RESOURCE');
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP('HIGH_GROUP','CONSUMER GROUP WITH HIGH RESOURCE');
    ```

### Step2: Create Resource Plan

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_PLAN to create a resource plan.

```plsql
-- Create a resource plan named RESPLAN
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN('RESPLAN','PLAN FOR RESOURCE TEST');
```

### Step3: Create Resource Plan Directive

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

### Step4: Create User Mapping

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).SET_CONSUMER_GROUP_MAPPING to create user mapping.

If the corresponding user does not exist, please [create the user](../../Product Security/Identity Identification and Authentication/Managing Users) first.

```plsql
-- Map user RESMAN1 to LOW_GROUP resource consumer group and user RESMAN2 to HIGH_GROUP resource consumer group, 
-- executed only by SYS user with privilege
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING('USER','RESMAN1','LOW_GROUP');
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING('USER','RESMAN2','HIGH_GROUP');
```

<span id="Activate" name="Activate"></span>

### Step5: Activate and Enable Resource Management

1. Log in to the database installation server using the installation user.


2. Modify the `RESOURCE_MANAGER_PLAN` parameter using the *yasboot* tool to activate the resource plan RESPLAN.

    ```shell
    # -r parameter indicates rollback of cluster parameters on modification failure
    $ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v RESPLAN -d -r

    # Check if the RESOURCE_MANAGER_PLAN parameter has taken effect on all nodes
    $ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
    ```

    > **Note**:
    >
    > The `RESOURCE_MANAGER_PLAN` parameter can only be modified using the `yasboot cluster config set` command.

3. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


4. Check if the resource plan is successfully activated. A VALUE of RESPLAN for the `RESOURCE_MANAGER_PLAN` parameter indicates successful activation.

    ```sql
    show parameter RESOURCE_MANAGER_PLAN;

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    RESOURCE_MANAGER_PLAN                                            RESPLAN
    ```

5. Modify the `RSRC_MODE` parameter using the *yasboot* tool to enable memory (MEM) or CPU resource management functionality, then restart the database to apply changes.

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

6. After the restart, connect to the database and check if the resource management functionality is successfully enabled. A VALUE of ALL for the `RSRC_MODE` parameter indicates successful enabling.

    ```sql
    show parameter RSRC_MODE;

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    RSRC_MODE                                                        ALL
    ```

After successfully enabling resource management functionality with the above example, when the user RESMAN1 connects to the database, it will be constrained by the directives of the LOW_GROUP resource consumer group under the resource plan RESPLAN.

> **Warn**:
>
> When resource management is enabled, restarting the server may cause the CPU and IO configuration in the `/sys/fs/cgroup/cgroup.subtree_control` file to be lost, resulting in the database failing to start (at this point, the run.log will record the error `cgroup directory %s is not existed, please create cgroup directory first`). When encountering this issue, execute the following commands to restore CPU and IO functionality before starting the database.
>
> ```shell
> $ sudo sh -c 'find /sys/fs/cgroup -name "cgroup.procs" -exec cat {} \; 2>/dev/null | sort -u | while read pid; do [ -n "$pid" ] && echo $pid 2>/dev/null; done > /sys/fs/cgroup/cgroup.procs'
> $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> ```

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
select RESNAME, NR_THROTTLED, THROTTLED_TIME from GV$CPUSTAT where RESNAME = 'LOW_GROUP';
```

### Monitor SPA Memory Resource Usage

YashanDB provides dynamic views to monitor the SPA memory usage of resource consumer groups and sessions.

#### Monitor SPA Memory Usage of Resource Consumer Groups

YashanDB provides dynamic views [V$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_CONSUMER_GROUP) and [GV$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_CONSUMER_GROUP) to monitor the SPA memory usage of resource consumer groups across the cluster.

```sql
-- Monitor SPA memory usage of HIGH_GROUP resource consumer group on all nodes
select SPA_LIMIT_QUOTA, SESSION_SPA_LIMIT_QUOTA, SPA_REMAIN_QUOTA, SPA_MAX_USE_QUOTA, SESSION_SPA_RESERVED_QUOTA, SPA_LIMIT_EXCEED_TIMES, 
SESSION_SPA_LIMIT_EXCEED_TIMES from GV$RSRC_CONSUMER_GROUP where NAME = 'HIGH_GROUP';
```

#### Monitor SPA Memory Usage of Sessions

YashanDB provides dynamic views [V$SESSION_SPA](../../Reference Manual/System Views/Dynamic Performance Views/V$SESSION_SPA) and [GV$SESSION_SPA](../../Reference Manual/System Views/Dynamic Performance Views/GV$SESSION_SPA) to monitor the SPA memory usage of sessions across the cluster.

```sql
-- Monitor SPA memory usage of the current session
select B.*, A.SPA_USE_MEM, A.SPA_USE_QUOTA, A.RESOURCE_CONSUMER_GROUP 
from V$SESSION A, V$SESSION_SPA B where A.SID = B.SID AND B.SID = USERENV('SID');
```

#### Monitor Execution Retries

In ISC Distributed Cluster Deployment, execution retry conditions can be monitored using the following statement.

```sql
-- Monitor the execution retry conditions of statements in ISC Distributed Cluster Deployment
select GROUP_ID,GROUP_NODE_ID,SID,GLOBAL_SESSION_ID,SERIAL#,SPA_USE_MEM,SPA_USE_QUOTA,SPA_MAX_USE_MEM,RESOURCE_CONSUMER_GROUP,
EXEC_START_TIME,RETRY_CNT,RETRY_INFO,EXEC_STATUS from GV$SESSION where type='USER';
```

### Monitor Parallel Execution Resource Usage

YashanDB provides dynamic views [V$PX_RES_MGR](../../Reference Manual/System Views/Dynamic Performance Views/V$PX_RES_MGR) and [GV$PX_RES_MGR](../../Reference Manual/System Views/Dynamic Performance Views/GV$PX_RES_MGR) to monitor parallel execution resource usage of resource consumer groups across the cluster.

```sql
-- Monitor parallel execution resource usage of HIGH_GROUP resource consumer group on all nodes
select GROUP_ID, GROUP_NODE_ID, PX_RES_TYPE, MAX_PX_RES_USAGE, CURR_PX_RES_USAGE from GV$PX_RES_MGR where CONSUMER_GROUP = 'HIGH_GROUP';
```

### Monitor Execution Scheduling within Resource Consumer Groups

YashanDB provides dynamic views [V$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_CONSUMER_GROUP) and [GV$RSRC_CONSUMER_GROUP](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_CONSUMER_GROUP) to monitor execution scheduling conditions of resource consumer groups across the cluster.

```sql
-- Monitor execution scheduling conditions of HIGH_GROUP resource consumer group on all nodes
select CONCURRENCY_LIMIT, CONCURRENCY_NUM, EXECUTION_WAITERS, REQUESTS, QUEUE_NUMBER, CONCURRENCY_LIMIT_HIT, 
QUEUED_TIME, QUEUE_TIMEOUTS, PARALLEL_DOWNGRADE_TIMES from GV$RSRC_CONSUMER_GROUP where NAME = 'HIGH_GROUP';
```

### Monitor Execution Scheduling within Sessions

YashanDB provides dynamic views [V$RSRC_SESSION_INFO](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_SESSION_INFO) and [GV$RSRC_SESSION_INFO](../../Reference Manual/System Views/Dynamic Performance Views/GV$RSRC_SESSION_INFO) to monitor execution scheduling conditions of sessions across the cluster.

```sql
-- Monitor execution scheduling conditions of all sessions under HIGH_GROUP resource consumer group on all nodes
select QUEUE_ID, CURRENT_QUEUED_TIME, QUEUE_TIMEOUTS, PARALLEL_DOWNGRADE_TIMES from GV$RSRC_SESSION_INFO where CURRENT_CONSUMER_GROUP = 'HIGH_GROUP';
```

## Disable Resource Management

To disable resource management, follow these steps:

- To delete resource consumer groups, ensure that the relevant mapping relationships and resource plan directives are deleted first, or an error will be returned.
- To delete a resource plan, ensure that the resource plan is not active and delete all resource plan directives associated with the resource plan first, or an error will be returned.

### Disable Resource Management

1. Log in to the database installation server using the installation user.


2. Use the *yasboot* tool to set the `RESOURCE_MANAGER_PLAN` parameter to disable all resource plans.

    ```shell
    $ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v '' -d -r
    # Check the results of the RSRC_MODE parameter modification across all nodes
    $ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
    ```

3. Use the *yasboot* tool to set the `RSRC_MODE` parameter to disable resource management functionality and restart the database for the changes to take effect.

    ```shell
    $ yasboot cluster config set -c yashandb -k RSRC_MODE -v NONE -d
    $ yasboot cluster restart -c yashandb
    # Check the results of the RSRC_MODE parameter modification across all nodes
    $ yasboot cluster config show -c yashandb -q RSRC_MODE
    ```

### Delete User Mapping

>**Note**:
>
>- When [deleting users](../../Product Security/Identity Identification and Authentication/Managing Users), the user's mapping relationships will be deleted.
>- When deleting a specific user's mapping relationship, the user will be automatically mapped to the DEFAULT_CONSUMER_GROUP.
>- The mapping relationship of the system user SYS cannot be deleted.

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_CONSUMER_GROUP_MAPPING to delete user mapping relationships.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING('USER','RESMAN1');
    EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING('USER','RESMAN2');
    ```

### Delete Resource Plan Directives

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_PLAN_DIRECTIVE to delete resource plan directives.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE('RESPLAN','LOW_GROUP');
    EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE('RESPLAN','HIGH_GROUP');
    ```

### Delete Resource Plan

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_PLAN to delete a resource plan.

    If the resource plan still has other directives, an error will be returned.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN('RESPLAN');
    ```

### Delete Resource Consumer Groups

1. Log in to the database as the sys user.
    
    ```shell
    # OS authentication (for local login)
    $ yasql / as sysdba

    # Password authentication
    $ yasql sys/********@192.168.1.2:1688

    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).DELETE_CONSUMER_GROUP to delete resource consumer groups.

    If the resource consumer group still has other consumers, an error will be returned.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP('LOW_GROUP');
    EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP('HIGH_GROUP');
    ```
