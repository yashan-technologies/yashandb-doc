This chapter will introduce global resource management in CDBs. For PDB local resource management, please refer to [Configure Resource Management](./Configuring Resource Management).

## Environment Preparation

Before using **CPU** resource management, you must ensure that the server has CGroups enabled.



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



## Enable Resource Management

### Step1: (Optional) Configure Resource Upper Limit Base Values

Please modify the corresponding configuration parameters to appropriate values according to whether IOPS resources, memory resources, and corresponding resource quota planning are enabled.

In Standalone Primary-Standby Deployment, the following parameters can be configured independently in both the primary and standby CDB roots, and then differential resource upper limit values can be generated according to the resource plan.

|Configuration Parameter |Resource |Description |Default Value |
| ----------------------------- | ----------------| -------------------------- | ---------------------- |
| [MAX_BPS](../../Reference Manual/Configuration Parameters.md#max_bps) | IOPS Resource  | Specifies the upper limit base value for read/write bytes per second allowed by PDB in a CDB. When CDB global resource management is enabled, this value will be used to calculate the upper limit of read/write bytes per second available to the target PDB, with the calculation formula being MAX_BPS * UTILIZATION_LIMIT | Defaults to 0, meaning no restrictions on |
| [MAX_IOPS](../../Reference Manual/Configuration Parameters.md#max_iops) | IOPS Resource  | Specifies the upper limit base value for PDB I/O operations per second in a CDB. When CDB global resource management is enabled, this value will be used to calculate the upper limit of I/O operations per second for the target PDB, with the calculation formula being MAX_IOPS * UTILIZATION_LIMIT | Defaults to 0, meaning no restrictions on  |
| [CDB_MAX_MEMORY_SIZE](../../Reference Manual/Configuration Parameters.md#cdb_max_memory_size) | Memory Resource  | Specifies the upper limit of the total physical memory available to all containers (the CDB root, all PDBs) in a CDB. When CDB global resource management is enabled, this value will be used to calculate the upper/lower limits of physical memory available to the target PDB, with the calculation formula being CDB_MAX_MEMORY_SIZE * corresponding percentage | Defaults to 0, meaning no restrictions on  |

1. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. View the current configuration.

    ```sql
    SHOW PARAMETER MAX_BPS

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    MAX_BPS                                                          0

    SHOW PARAMETER MAX_IOPS

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    MAX_IOPS                                                         0

    SHOW PARAMETER CDB_MAX_MEMORY_SIZE

    NAME                                                             VALUE                                                  
    ---------------------------------------------------------------- ----------------------------------------------------------------
    CDB_MAX_MEMORY_SIZE                                              0
    ```

3. Modify the values of corresponding parameters as needed.

    ```sql
    ALTER SYSTEM SET MAX_IOPS=2147483647;
    ```

    >**Note**:
    >
    > In Standalone Primary-Standby Deployment, you can also repeat the above operations on the standby CDB root as needed to configure differential parameter values.

### Step2: Create CDB Resource Plan Directives

1. Log in to the CDB root as the sys user.
    
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
    

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN to create a resource plan.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
        'RES_CDB_PLAN',
        'RESOURCE PLAN FOR CDB MANAGEMENT');
    ```

### Step3: Associate CDB Resource Plan Directives with PDB

#### Method 1: Configure Dedicated CDB Resource Plan Directives

Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_CDB_PLAN_DIRECTIVE to create a resource plan directive.

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'PDB1',
    COMMENT => 'RESOURCE PLAN DIRECTIVE FOR PLUGGABLE DATABASE PDB1',
    SHARES => 25,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 100,
    MEMORY_LIMIT => 50,
    MEMORY_MIN => 20);
```

#### Method 2: Associate CDB Resource Template Directives with PDB

1. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).CREATE_CDB_PROFILE_DIRECTIVE to create CDB resource template directives.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PROFILE_DIRECTIVE(
        PLAN => 'RES_CDB_PLAN',
        PROFILE => 'RES_CDB_PROFILE',
        COMMENT => 'RESOURCE PROFILE DIRECTIVE',
        SHARES => 10,
        UTILIZATION_LIMIT => 30,
        PARALLEL_SERVER_LIMIT => 50,
        MEMORY_LIMIT => 25,
        MEMORY_MIN => 10);
    ```

2. Call [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER).SET_PDB_PROFILE to set the mapping relationship between PDB and CDB resource template directives.

    ```plsql
    EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
        PLUGGABLE_DATABASE => 'PDB1','PDB2',
        PROFILE => 'RES_CDB_PROFILE'); 
    ```

### Step4: Activate and Enable Resource Management

1. Log in to the database installation server using the installation user.


2. Modify the RESOURCE_MANAGER_PLAN parameter using the *yasboot* tool to activate the resource plan RES_CDB_PLAN.

    ```shell
    # -r parameter indicates rollback of cluster parameters on modification failure
    $ yasboot cluster config set -c yashandb -k RESOURCE_MANAGER_PLAN -v RES_CDB_PLAN -d -r

    # Check if the RESOURCE_MANAGER_PLAN parameter has taken effect on all nodes
    $ yasboot cluster config show -c yashandb -q RESOURCE_MANAGER_PLAN
    ```

    > **Note**:
    >
    > The `RESOURCE_MANAGER_PLAN` parameter can only be modified using the `yasboot cluster config set` command.


3. Connect and log in to the CDB root as a DBA user.
    ```shell
    $ yasql c##sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


4. Check whether the RESOURCE_MANAGER_PLAN parameter configuration have taken effect.

    ```sql
    show parameter RESOURCE_MANAGER_PLAN;

    NAME                                                             VALUE
    ---------------------------------------------------------------- ----------------------------------------------------------------
    RESOURCE_MANAGER_PLAN                                            RES_CDB_PLAN                                                      ALL
    ```

After successfully enabling the CDB global resource management function through the above example, [local resource management configuration](./Configuring Resource Management) can also be performed by directly connecting to PDBs as needed.

> **Warn**:
>
> When resource management is enabled, restarting the server may cause the CPU and IO configuration in the `/sys/fs/cgroup/cgroup.subtree_control` file to be lost, resulting in the database failing to start (at this point, the run.log will record the error `cgroup directory %s is not existed, please create cgroup directory first`). When encountering this issue, execute the following commands to restore CPU and IO functionality before starting the database.
>
> ```shell
> $ sudo sh -c 'find /sys/fs/cgroup -name "cgroup.procs" -exec cat {} \; 2>/dev/null | sort -u | while read pid; do [ -n "$pid" ] && echo $pid 2>/dev/null; done > /sys/fs/cgroup/cgroup.procs'
> $ echo "+cpu" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> $ echo "+io" | sudo tee /sys/fs/cgroup/cgroup.subtree_control
> ```

## View CDB Resource Configuration

All CDB resource plans can be queried through the [DBA_CDB_RSRC_PLANS](../../Reference Manual/System Views/DBA Views/DBA_CDB_RSRC_PLANS) view.

```sql
select plan,comments from dba_cdb_rsrc_plans;
PLAN                                                             COMMENTS
---------------------------------------------------------------- -----------------------------------------------------------
DEFAULT_CDB_PLAN                                                 DEFAULT RESOURCE PLAN FOR CDB$ROOT
```

All CDB resource plan directives can be viewed through the [DBA_CDB_RSRC_PLAN_DIRECTIVES](../../Reference Manual/System Views/DBA Views/DBA_CDB_RSRC_PLAN_DIRECTIVES) view.

```sql
select plan, pluggable_database, shares, utilization_limit, memory_min, memory_limit from dba_cdb_rsrc_plan_directives;
PLAN                                                             PLUGGABLE_DATABASE                                                    SHARES UTILIZATION_LIMIT  MEMORY_MIN MEMORY_LIMIT
---------------------------------------------------------------- ---------------------------------------------------------------- ----------- ----------------- ----------- ------------
DEFAULT_CDB_PLAN                                                                                                                            1               100           1          100
RES_CDB_PLAN                                                                                                                                1               100           1          100
RES_CDB_PLAN                                                     PDB1                                                                      25                50          20           50
```

## View PDB Resource Usage

The resource usage of all PDBs can be viewed through [V$RSRC_PDB](../../Reference Manual/System Views/Dynamic Performance Views/V$RSRC_PDB).

```sql
select pdb_name, cpu_wait_time, cpu_waits, consumed_cpu_time, sga_bytes, buffer_cache_bytes, shared_pool_bytes from v$rsrc_pdb;
```
