The DBMS_RESOURCE_MANAGER package provides a set of [resource management](../../../Database Administration/Resource Management/00Resource Management) stored procedures/functions for creating and deleting resource plans, resource directives, resource mappings, and related operations.

> **Note**:
>
> - Only SYS users can call the stored procedures/functions of this advanced package.
> - This advanced package is not applicable to YAC/Distributed Cluster Deployment.
> - In ISC Distributed Cluster Deployment, all stored procedures/functions in this advanced package are only allowed to be called one by one on CN nodes.

The stored procedures/functions of this advanced package apply to the following objects:

| Action Scope | Subprograms | Applicable Objects        |
|--------------------|--------------------------------------|--------------------|
| CDB global resource management   | [CREATE_CDB_PLAN](#CREATE_CDB_PLAN)<br />[CREATE_CDB_PLAN_DIRECTIVE](#CREATE_CDB_PLAN_DIRECTIVE)<br />[CREATE_CDB_PROFILE_DIRECTIVE](#CREATE_CDB_PROFILE_DIRECTIVE)<br />[SET_PDB_PROFILE](#SET_PDB_PROFILE)<br />[UPDATE_CDB_DEFAULT_DIRECTIVE](#UPDATE_CDB_DEFAULT_DIRECTIVE)<br />[UPDATE_CDB_PLAN_DIRECTIVE](#UPDATE_CDB_PLAN_DIRECTIVE)<br />[UPDATE_CDB_PROFILE_DIRECTIVE](#UPDATE_CDB_PROFILE_DIRECTIVE)<br />[DELETE_CDB_PLAN_DIRECTIVE](#DELETE_CDB_PLAN_DIRECTIVE)<br />[DELETE_CDB_PROFILE_DIRECTIVE](#DELETE_CDB_PROFILE_DIRECTIVE)<br />[DELETE_CDB_PLAN](#DELETE_CDB_PLAN) | Can only be executed by directly connecting to the CDB root in Standalone Deployment CDB<br/>If it is a high-availability deployment, the primary CDB root must be directly connected, and resource management currently only takes effect on the primary CDB   |
| Non-CDB resource management<br/>CDB local resource management   | [CREATE_CONSUMER_GROUP](#CREATE_CONSUMER_GROUP)<br />[CREATE_PLAN](#CREATE_PLAN)<br />[CREATE_PLAN_DIRECTIVE](#CREATE_PLAN_DIRECTIVE)<br />[SET_CONSUMER_GROUP_MAPPING](#SET_CONSUMER_GROUP_MAPPING)<br />[UPDATE_PLAN_DIRECTIVE](#UPDATE_PLAN_DIRECTIVE)<br />[DELETE_CONSUMER_GROUP_MAPPING](#DELETE_CONSUMER_GROUP_MAPPING)<br />[DELETE_PLAN_DIRECTIVE](#DELETE_PLAN_DIRECTIVE)<br />[DELETE_PLAN](#DELETE_PLAN)<br />[DELETE_CONSUMER_GROUP](#DELETE_CONSUMER_GROUP) | * Non-CDB<br/>* PDBs (excluding PDB seed)   |

<span id="CREATE_CDB_PLAN" name="CREATE_CDB_PLAN" class="yaslink"></span>

## CREATE\_CDB\_PLAN

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
    PLAN             IN    VARCHAR(64),
    COMMENT          IN    VARCHAR(2000) DEFAULT NULL);
```

This program is used to create a CBD resource plan. Currently, it only supports the creation of top-level plans and does not support creating sub-plans.

In a CDB, the system includes a default resource plan DEFAULT_CDB_PLAN, whose directive resource management parameters are all default values, and it is associated with all PDBs by default. 



|Parameter |Description |
| --- | --- |
| PLAN | CBD resource plan name, name must be unique and comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers), reserved keywords are allowed, but do not meet [the general rules for double quotes](../../SQL Reference Manual/Basic SQL Elements/Double Quotes)  |
| COMMENT | Comment |



***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN(
   PLAN => 'RES_CDB_PLAN',
   COMMENT => 'RESOURCE PLAN FOR CDB MANAGEMENT');
```

<span id="CREATE_CDB_PLAN_DIRECTIVE" name="CREATE_CDB_PLAN_DIRECTIVE" class="yaslink"></span>

## CREATE\_CDB\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

This program is used to create CDB resource plan directives and simultaneously associate the directive with the specified PDB.  

Resource plan directives can only be associated with one PDB. If you need to configure the same resource quota for multiple PDBs, you can use [CDB resource template directives](#CREATE_CDB_PROFILE_DIRECTIVE).  

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of an existing CDB resource plan                           |
| PLUGGABLE_DATABASE    | General Parameters     | PDB name                         |
| COMMENT               | General Parameters     | Comment information                       |
| SHARES                | CPU/IOPS Resource Management Parameters | CPU/IOPS usage share, integer value in [1,100] or NULL. Default NULL means minimum value 1  |
| UTILIZATION_LIMIT     | CPU/IOPS Resource Management Parameters| Maximum CPU/IOPS usage limit (unit: %), integer value in [1,100] or NULL. Default NULL means maximum value 100  |
| PARALLEL_SERVER_LIMIT | Parallel Execution Resource Management Parameters | Maximum parallel resource usage percentage (unit: %), integer value in [0,100] or NULL. Default NULL means maximum value 100.  |
| MEMORY_LIMIT          | Memory Management Parameters | Maximum memory usage percentage (unit: %), integer value in [1,100] or NULL. Default NULL means maximum value 100. This value must be greater than or equal to MEMORY_MIN, and over-allocation is allowed.  |
| MEMORY_MIN            | Memory Management Parameters | Minimum memory usage percentage (unit: %), integer value in [1,100] or NULL. Default NULL means minimum value 1. This value must be less than or equal to MEMORY_LIMIT, and over-allocation is not allowed.  |



Usage Instructions:

- The CPU upper limit calculation formula is `UTILIZATION_LIMIT * Number of CPUs`. For example, if the MAX_UTILIZATION_LIMIT in the resource consumer group mapped to a user is 10, and the number of CPUs in each node of the environment is 2, then the maximum CPU usage rate for that user on each node is 20%.

- IOPS resource items and their upper limit calculation formulas are as follows:
    
    - Maximum read/write bytes per second: Calculation formula is `UTILIZATION_LIMIT * MAX_BPS`, where the MAX_BPS parameter defaults to 0, meaning no restrictions on corresponding resources.

    - Maximum I/O operations per second: Calculation formula is `UTILIZATION_LIMIT * MAX_IOPS`, where the MAX_IOPS parameter defaults to 0, meaning no restrictions on corresponding resources.

- The memory upper and lower limits are calculated by multiplying CDB_MAX_MEMORY_SIZE by the corresponding percentage. PDB usage is only based on memory-related parameter configuration values (not current actual usage), including DATA_BUFFER_SIZE, SHARE_POOL_SIZE, COLUMNAR_BUFFER_SIZE, etc.

    - If PDB usage configuration > upper limit CDB_MAX_MEMORY_SIZE * MEMORY_LIMIT, the PDB cannot be started. If the PDB is running, the corresponding configuration cannot be adjusted to exceed the upper limit.

    - If PDB usage configuration < lower limit CDB_MAX_MEMORY_SIZE * MEMORY_MIN, it does not affect PDB operation, but an alert log will be generated for recording.



***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.CREATE_CDB_PLAN_DIRECTIVE(
   PLAN => 'RES_CDB_PLAN',
   PLUGGABLE_DATABASE => 'pdb1',
   COMMENT => 'RESOURCE PLAN DIRECTIVE FOR PLUGGABLE DATABASE pdb1',
   SHARES => 25,
   UTILIZATION_LIMIT => 50,
   PARALLEL_SERVER_LIMIT => 100,
   MEMORY_LIMIT => 50,
   MEMORY_MIN => 20);
```

<span id="CREATE_CDB_PROFILE_DIRECTIVE" name="CREATE_CDB_PROFILE_DIRECTIVE" class="yaslink"></span>

## CREATE\_CDB\_PROFILE\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

This program is used to create CDB resource template directives. Through CDB resource template directives, the same resource quotas can be simultaneously [associated](#SET_PDB_PROFILE) with multiple PDBs.

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of an existing CDB resource plan                           |
|PROFILE|General Parameters|Name of the CDB resource template|
|COMMENT|General Parameters|Comment information|
|SHARES                | CPU/IOPS Resource Management Parameters | CPU/IOPS usage share, integer value in [1,100] or NULL. Default NULL means minimum value 1  |
| UTILIZATION_LIMIT     | CPU/IOPS Resource Management Parameters| Maximum CPU/IOPS usage limit (unit: %), integer value in [1,100] or NULL. Default NULL means maximum value 100  |
| PARALLEL_SERVER_LIMIT | Parallel Execution Resource Management Parameters | Maximum parallel resource usage percentage (unit: %), integer value in [0,100] or NULL. Default NULL means maximum value 100.  |
| MEMORY_LIMIT          | Memory Management Parameters | Maximum memory usage percentage (unit: %), integer value in [1,100] or NULL. Default NULL means maximum value 100. This value must be greater than or equal to MEMORY_MIN, and over-allocation is allowed.  |
| MEMORY_MIN            | Memory Management Parameters | Minimum memory usage percentage (unit: %), integer value in [1,100] or NULL. Default NULL means minimum value 1. This value must be less than or equal to MEMORY_LIMIT, and over-allocation is not allowed.  |



Usage Instructions:

- The CPU upper limit calculation formula is `UTILIZATION_LIMIT * Number of CPUs`. For example, if the MAX_UTILIZATION_LIMIT in the resource consumer group mapped to a user is 10, and the number of CPUs in each node of the environment is 2, then the maximum CPU usage rate for that user on each node is 20%.

- IOPS resource items and their upper limit calculation formulas are as follows:
    
    - Maximum read/write bytes per second: Calculation formula is `UTILIZATION_LIMIT * MAX_BPS`, where the MAX_BPS parameter defaults to 0, meaning no restrictions on corresponding resources.

    - Maximum I/O operations per second: Calculation formula is `UTILIZATION_LIMIT * MAX_IOPS`, where the MAX_IOPS parameter defaults to 0, meaning no restrictions on corresponding resources.

- The memory upper and lower limits are calculated by multiplying CDB_MAX_MEMORY_SIZE by the corresponding percentage. PDB usage is only based on memory-related parameter configuration values (not current actual usage), including DATA_BUFFER_SIZE, SHARE_POOL_SIZE, COLUMNAR_BUFFER_SIZE, etc.

    - If PDB usage configuration > upper limit CDB_MAX_MEMORY_SIZE * MEMORY_LIMIT, the PDB cannot be started. If the PDB is running, the corresponding configuration cannot be adjusted to exceed the upper limit.

    - If PDB usage configuration < lower limit CDB_MAX_MEMORY_SIZE * MEMORY_MIN, it does not affect PDB operation, but an alert log will be generated for recording.



***Example*** for Standalone Deployment

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

<span id="SET_PDB_PROFILE" name="SET_PDB_PROFILE" class="yaslink"></span>

## SET\_PDB\_PROFILE

```plsql
DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64) DEFAULT NULL);
```

This program is used to set the mapping relationship between PDB and CDB resource template directives.

The priority of various directives taking effect on PDBs is CDB resource plan directive (CDB_PLAN_DIRECTIVE) > CDB resource plan template (CDB_PROFILE_DIRECTIVE) > CDB default resource plan directive (CDB_DEFAULT_DIRECTIVE).



|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLUGGABLE_DATABASE    | General Parameters     | PDB name, only one PDB can be specified at a time.     |
| PROFILE            | General Parameters | Name of the CDB resource template. Default value NULL indicates removing the mapping relationship from PDB to resource template   |

***Example*** for Standalone Deployment

```plsql
-- Set resource profile for PDB
EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE => 'pdb1',
    PROFILE => 'RES_CDB_PROFILE');

-- Delete resource profile for PDB
EXEC DBMS_RESOURCE_MANAGER.SET_PDB_PROFILE(
    PLUGGABLE_DATABASE => 'pdb1');
```

<span id="UPDATE_CDB_DEFAULT_DIRECTIVE" name="UPDATE_CDB_DEFAULT_DIRECTIVE" class="yaslink"></span>

## UPDATE\_CDB\_DEFAULT\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_DEFAULT_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

This procedure is used to update the configuration of CDB default resource plan directives. The initial configuration is:

- SHARES = 1
- UTILIZATION_LIMIT = 100
- MEMORY_LIMIT = 100
- MEMORY_MIN = 1


|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of the CDB default resource plan         |
|COMMENT    |   General Parameters  |   Comment information |
|  SHARES                | CPU/IOPS Resource Management Parameters | CPU/IOPS usage share, integer value in [1,100] or NULL   |
| UTILIZATION_LIMIT     | CPU/IOPS Resource Management Parameters| Maximum CPU/IOPS usage limit (unit: %), integer value in [1,100] or NULL   |
| PARALLEL_SERVER_LIMIT | Parallel Execution Resource Management Parameters | Maximum parallel resource usage percentage (unit: %), integer value in [0,100] or NULL   |
| MEMORY_LIMIT          | Memory Management Parameters | Maximum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be greater than or equal to MEMORY_MIN, and over-allocation is allowed  |
| MEMORY_MIN            | Memory Management Parameters | Minimum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be less than or equal to MEMORY_LIMIT, and over-allocation is not allowed  |

Usage Instructions:

-  Optional parameters that are not assigned values or input as NULL will not be processed.



***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_DEFAULT_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    COMMENT => 'DEFAULT DIRECTIVE FOR RES_CDB_PLAN',
    SHARES => 10,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 25,
    MEMORY_LIMIT => 50,
    MEMORY_MIN => 25);
```

<span id="UPDATE_CDB_PLAN_DIRECTIVE" name="UPDATE_CDB_PLAN_DIRECTIVE" class="yaslink"></span>

## UPDATE\_CDB\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

This program is used to update the configuration information of CDB resource plan directives.  

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of the CDB resource plan to which the CDB resource plan directive to be updated belongs           |
| PLUGGABLE_DATABASE    | General Parameters     | PDB name. If the PDB name is updated, it indicates unbinding the plan directive from the original PDB and associating it with another PDB                 |
| COMMENT               | General Parameters     | Comment information               |
|  SHARES                | CPU/IOPS Resource Management Parameters | CPU/IOPS usage share, integer value in [1,100] or NULL   |
| UTILIZATION_LIMIT     | CPU/IOPS Resource Management Parameters| Maximum CPU/IOPS usage limit (unit: %), integer value in [1,100] or NULL   |
| PARALLEL_SERVER_LIMIT | Parallel Execution Resource Management Parameters | Maximum parallel resource usage percentage (unit: %), integer value in [0,100] or NULL   |
| MEMORY_LIMIT          | Memory Management Parameters | Maximum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be greater than or equal to MEMORY_MIN, and over-allocation is allowed  |
| MEMORY_MIN            | Memory Management Parameters | Minimum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be less than or equal to MEMORY_LIMIT, and over-allocation is not allowed  |

Usage Instructions:

-  Optional parameters that are not assigned values or input as NULL will not be processed.

 

***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_PLAN_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'pdb1',
    COMMENT => NULL,
    SHARES => 50,
    UTILIZATION_LIMIT => 50,
    PARALLEL_SERVER_LIMIT => 50,
    MEMORY_LIMIT => 100,
    MEMORY_MIN => 50);
```

<span id="UPDATE_CDB_PROFILE_DIRECTIVE" name="UPDATE_CDB_PROFILE_DIRECTIVE" class="yaslink"></span>

## UPDATE\_CDB\_PROFILE\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64),
    COMMENT                       IN    VARCHAR(2000) DEFAULT NULL,
    SHARES                        IN    NUMBER        DEFAULT NULL,
    UTILIZATION_LIMIT             IN    NUMBER        DEFAULT NULL,
    PARALLEL_SERVER_LIMIT         IN    NUMBER        DEFAULT NULL,
    MEMORY_LIMIT                  IN    NUMBER        DEFAULT NULL,
    MEMORY_MIN                    IN    NUMBER        DEFAULT NULL);
```

This program is used to update the directive information of CDB resource plan templates.  

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of the CDB resource plan to which the CDB resource template directive to be updated belongs     |
|PROFILE|General Parameters|Name of target CDB resource template directive|
|COMMENT|General Parameters|Comment information|
|  SHARES                | CPU/IOPS Resource Management Parameters | CPU/IOPS usage share, integer value in [1,100] or NULL   |
| UTILIZATION_LIMIT     | CPU/IOPS Resource Management Parameters| Maximum CPU/IOPS usage limit (unit: %), integer value in [1,100] or NULL   |
| PARALLEL_SERVER_LIMIT | Parallel Execution Resource Management Parameters | Maximum parallel resource usage percentage (unit: %), integer value in [0,100] or NULL   |
| MEMORY_LIMIT          | Memory Management Parameters | Maximum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be greater than or equal to MEMORY_MIN, and over-allocation is allowed  |
| MEMORY_MIN            | Memory Management Parameters | Minimum memory usage percentage (unit: %), integer value in [1,100] or NULL. This value must be less than or equal to MEMORY_LIMIT, and over-allocation is not allowed  |

Usage Instructions:

-  Optional parameters that are not assigned values or input as NULL will not be processed.

 


***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.UPDATE_CDB_PROFILE_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PROFILE => 'RES_CDB_PROFILE',
    COMMENT => NULL,
    SHARES => 50,
    UTILIZATION_LIMIT => 100,
    PARALLEL_SERVER_LIMIT => 100,
    MEMORY_LIMIT => 100,
    MEMORY_MIN => 50);
```

<span id="DELETE_CDB_PLAN_DIRECTIVE" name="DELETE_CDB_PLAN_DIRECTIVE" class="yaslink"></span>

## DELETE\_CDB\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PLUGGABLE_DATABASE            IN    VARCHAR(64));
```

This program is used to delete CDB resource plan directives. After successful deletion, the corresponding PDB will use the associated [PROFILE_DIRECTIVE](#SET_PDB_PROFILE) or default directive. 

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of the CDB resource plan to which the CDB resource plan directive to be deleted belongs     |
| PLUGGABLE_DATABASE    | General Parameters     | Name of the PDB associated with the CDB resource plan directive to be deleted    |


***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN_DIRECTIVE(
    PLAN = > 'RES_CDB_PLAN',
    PLUGGABLE_DATABASE => 'pdb1');
```

<span id="DELETE_CDB_PROFILE_DIRECTIVE" name="DELETE_CDB_PROFILE_DIRECTIVE" class="yaslink"></span>

## DELETE\_CDB\_PROFILE\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PROFILE_DIRECTIVE(
    PLAN                          IN    VARCHAR(64),
    PROFILE                       IN    VARCHAR(64));
```

This program is used to delete CDB resource plan template directives. Before deletion, [SET_PDB_PROFILE](#SET_PDB_PROFILE) must be executed to disassociate the target resource template from the PDB.  


|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of the CDB resource plan to which the CDB resource template directive to be deleted belongs     |
|PROFILE|General Parameters|Name of the CDB resource template directive to be deleted |


***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PROFILE_DIRECTIVE(
    PLAN => 'RES_CDB_PLAN',
    PROFILE => 'RES_CDB_PROFILE');
```

<span id="DELETE_CDB_PLAN" name="DELETE_CDB_PLAN" class="yaslink"></span>

## DELETE\_CDB\_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN(
    PLAN             IN    VARCHAR(64));
```

This program is used to delete resource plans on the CDB root. It is not possible to delete active resource plans or the built-in resource plan DEFAULT_CDB_PLAN.  

|Parameter |Description |
| --- | --- |
| PLAN | Name of an existing CDB resource plan  |

***Example*** for Standalone Deployment

```plsql
EXEC DBMS_RESOURCE_MANAGER.DELETE_CDB_PLAN(
   PLAN => 'RES_CDB_PLAN');
```

<span id="CREATE_CONSUMER_GROUP" name="CREATE_CONSUMER_GROUP" class="yaslink"></span>

## CREATE\_CONSUMER\_GROUP

```plsql
DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP IN VARCHAR(64),
    COMMENT        IN VARCHAR(2000) DEFAULT NULL);
```

This program is used to create a resource consumer group.

The system includes the following default resource consumer groups:

-  SYS_GROUP: includes the system user sys and system processes; modifications and deletions of SYS_GROUP's configurations and mappings are not allowed.
- DEFAULT_CONSUMER_GROUP: resource consumers without resource mappings are defaulted to this group.

YashanDB supports a maximum of 126 user-defined resource consumer groups.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| CONSUMER_GROUP | Resource consumer group, name must be unique and comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers), reserved keywords are allowed, but do not meet [the general rules for double quotes](../../SQL Reference Manual/Basic SQL Elements/Double Quotes)               |
| COMMENT        | Comment |

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP1',
    'GROUP FOR CPU RESOURCE1');
-- Default comment
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP2');
-- Comment info is empty
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    'RESGROUP3',
    NULL);
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP4',
    COMMENT => 'GROUP FOR CPU RESOURCE4');
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP5');
EXEC DBMS_RESOURCE_MANAGER.CREATE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP6',
    COMMENT => NULL);
```

<span id="CREATE_PLAN" name="CREATE_PLAN" class="yaslink"></span>

## CREATE\_PLAN

```plsql
DBMS_RESOURCE_MANAGER.CREATE_PLAN (
    PLAN          IN   VARCHAR(64), 
    COMMENT       IN   VARCHAR(2000) DEFAULT NULL);
```

This program is used to create a resource plan. Currently, it only supports the creation of top-level plans and does not support creating sub-plans.

The system includes the following default resource plans:

-  TOALL: top-level resource plan that includes instructions linked to internal processes and DEFAULT_CONSUMER_GROUP.
- SYS_GROUP: a second-level resource plan, a sub-plan of TOALL, that includes instructions related to the system user sys and system processes.



|Parameter |Description |
| --- | --- |
| PLAN | CBD resource plan name, name must be unique and comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers), reserved keywords are allowed, but do not meet [the general rules for double quotes](../../SQL Reference Manual/Basic SQL Elements/Double Quotes)  |
| COMMENT | Comment |



***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN1',
    'PLAN FOR CPU RESOURCE1');
-- Default comment
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN2');
-- Comment info is empty
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    'RESPLAN3',
    NULL);
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN4',
    COMMENT => 'PLAN FOR CPU RESOURCE4');
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN5');
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN(
    PLAN => 'RESPLAN6',
    COMMENT => NULL);
```

<span id="CREATE_PLAN_DIRECTIVE" name="CREATE_PLAN_DIRECTIVE" class="yaslink"></span>

## CREATE\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN                    IN VARCHAR(64),
    GROUP_OR_SUBPLAN        IN VARCHAR(64),
    MGMT_P1                 IN NUMBER DEFAULT NULL,
    MAX_UTILIZATION_LIMIT   IN NUMBER DEFAULT NULL,
    UTILIZATION_LIMT        IN NUMBER DEFAULT NULL,
    SHARES                  IN NUMBER DEFAULT NULL,
    PARALLEL_SERVER_LIMIT   IN NUMBER DEFAULT NULL,
    SPA_LIMIT               IN NUMBER DEFAULT NULL,
    SPA_LIMIT_RESERVED      IN NUMBER DEFAULT NULL,
    SESSION_SPA_LIMIT       IN NUMBER DEFAULT NULL,
    EXECUTION_QUEUE_TIMEOUT IN NUMBER DEFAULT NULL
    CONCURRENCY_LIMIT       IN NUMBER  DEFAULT NULL);
```

This program is used to create resource plan directives.

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- |:----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of an existing resource plan                           |
| GROUP_OR_SUBPLAN        | General Parameter        | Resource consumer group                                     |
|   SHARES               | CPU Resource Management   | Share in CPU sharing mode (unit: %), must be an integer in [1,100], NULL indicates a minimum value of 1  |
| PARALLEL_SERVER_LIMIT   | Parallel Execution Resource Management | Maximum percentage of parallel resources that can be used by the consumer group, must be an integer in [0,100], NULL indicates a maximum value of 100 |
| UTILIZATION_LIMIT   | CPU Resource Management   | Maximum CPU usage limit (unit: %), must be an integer in [1,100], NULL indicates a maximum value of 100  |
| MAX_UTILIZATION_LIMIT       | CPU Resource Management   | Same as the field UTILIZATION_LIMIT                     |
| MGMT_P1                  | CPU Resource Management   | Same as the field SHARES                                   |
| SPA_LIMIT               | Memory Management         | Maximum percentage of user memory occupied by the consumer group (unit: %), public memory usage is not limited; must be an integer in [1,100], NULL indicates a maximum value of 100 |
| SPA_LIMIT_RESERVED      | Memory Management         | Percentage of user memory reserved within the consumer group as private parts for each session (unit: %). The reserved user memory does not require application to the consumer group; must be an integer in [0,100], NULL indicates a minimum value of 0 |
| SESSION_SPA_LIMIT       | Memory Management         | The maximum percentage of available memory occupied by the session in the resource group (unit: %). Must be a positive integer in [1,100], NULL indicates a maximum value of 100 |
| EXECUTION_QUEUE_TIMEOUT | Execution Scheduling Management | Timeout duration for waiting in the queue when resources are insufficient, unit is seconds; must be an integer in [-1, 4294967295], -1 indicates indefinite timeout, NULL equals to the minimum value of -1 |
| CONCURRENCY_LIMIT       | Execution Scheduling Management  | Number of resource-intensive SQLs allowed to execute concurrently within the consumer group, must be an integer in [0, 2147483647], NULL indicates no limit |

Usage Instructions:

-  When both MGMT_P1 and SHARES are set, the shares parameter takes precedence.
- When both MAX_UTILIZATION_LIMIT and UTILIZATION_LIMIT are set, the UTILIZATION_LIMIT parameter takes precedence.
- The maximum CPU usage rate calculation formula is `MAX_UTILIZATION_LIMIT * Number of CPUs`. For example, if the MAX_UTILIZATION_LIMIT in the resource consumer group mapped to a user is 10, and the number of CPUs in each node of the environment is 2, then the maximum CPU usage rate for that user on each node is 20%.
- To ensure that SYS_GROUP has sufficient resources available, if the shared resources (MGMT_P1) of that group are too few or too many, the system will dynamically adjust upon user input. If the global proportion of SYS_GROUP's MGMT_P1 is less than 40% or greater than 60%, the system will dynamically adjust SYS_GROUP's MGMT_P1 to the total MGMT_P1 of the remaining resource groups, at which point the MGMT_P1 value of SYS_GROUP may exceed 100 (you can check the MGMT_P1 field of the DBA_RSRC_PLAN_DIRECTIVES view; the global proportion calculation formula is: `SYS_GROUP's MGMT_P1 / Total MGMT_P1 of all resource consumer groups`).
- After upgrading from a lower version of the database, if the MGMT_P1 or MAX_UTILIZATION_LIMIT parameter was originally configured to 0, the actual performance will be the same as configuring it to 100 in the current version.
- In CDBs, if a PDB is configured with both global resource management and local resource management, the quotas for SHARES and UTILIZATION_LIMIT will be the product of global and local values. For example, if both global and local resource management plan directives are configured with UTILIZATION_LIMIT=80, the actual quota will be `80 * 80 = 64`.  

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Optional parameters not configured
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2',
    10,
    20,
    30,
    40,
    50);
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN3',
    GROUP_OR_SUBPLAN => 'RESGROUP3',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20,
    UTILIZATION_LIMIT => 30,
    SHARES => 40,
    PARALLEL_SERVER_LIMIT => 50);
-- Only MGMT_P1 and MAX_UTILIZATION_LIMIT parameters configured
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20);
-- Only PARALLEL_SERVER_LIMIT parameter configured
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5',
    PARALLEL_SERVER_LIMIT => 50);
-- Following example will raise an error as subsequent parameters can only continue specifying parameter names
EXEC DBMS_RESOURCE_MANAGER.CREATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN6',
    GROUP_OR_SUBPLAN => 'RESGROUP6',
    10);

YAS-04253 PL/SQL compiling errors:
[1:104] YAS-00003 invalid parameter, reason: param => input order error
```

<span id="SET_CONSUMER_GROUP_MAPPING" name="SET_CONSUMER_GROUP_MAPPING" class="yaslink"></span>

## SET\_CONSUMER\_GROUP\_MAPPING

```plsql
DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE      IN VARCHAR(64),
    VALUE          IN VARCHAR(64),
    CONSUMER_GROUP IN VARCHAR(64) DEFAULT NULL);
```

This program is used to map resource consumers to resource consumer groups. The mapping relationship between SYS user and SYS_GROUP group cannot be modified.

If there is no mapping, a mapping to the corresponding resource group will be created; if the mapping already exists, an attempt will be made to update the mapping to the new resource group.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| ATTRIBUTE      | Mapping attribute<br/>Only supports USER, that is, resource consumers dimensioned by users |
| VALUE          | The username to be mapped, must be an existing user     |
| CONSUMER_GROUP | The resource consumer group to be mapped; NULL indicates deletion of the corresponding mapping |

If the mapped resource consumer group does not have corresponding plan directives, it will behave the same as DEFAULT_CONSUMER_GROUP. When creating new plan directives, the user must log in again to take effect.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Default CONSUMER_GROUP parameter has the same effect as DELETE_CONSUMER_GROUP_MAPPING
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1');
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1',
    'RESGROUP1');
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.SET_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE => 'USER',
    VALUE => 'SALES2',
    CONSUMER_GROUP => 'RESGROUP2');
```

<span id="UPDATE_PLAN_DIRECTIVE" name="UPDATE_PLAN_DIRECTIVE" class="yaslink"></span>

## UPDATE\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN                    IN VARCHAR(64),
    GROUP_OR_SUBPLAN        IN VARCHAR(64),
    MGMT_P1                 IN NUMBER DEFAULT NULL,
    MAX_UTILIZATION_LIMIT   IN NUMBER DEFAULT NULL,
    UTILIZATION_LIMT        IN NUMBER DEFAULT NULL,
    SHARES                  IN NUMBER DEFAULT NULL,
    PARALLEL_SERVER_LIMIT   IN NUMBER DEFAULT NULL,
    SPA_LIMIT               IN NUMBER DEFAULT NULL,
    SPA_LIMIT_RESERVED      IN NUMBER DEFAULT NULL,
    SESSION_SPA_LIMIT       IN NUMBER DEFAULT NULL,
    EXECUTION_QUEUE_TIMEOUT IN NUMBER DEFAULT NULL
    CONCURRENCY_LIMIT       IN NUMBER  DEFAULT NULL);
```

This program is used to update resource plan directives.

|Parameter |Parameter Category |Description |
| :---------------------- | :------------- | :----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of an existing resource plan                           |
| GROUP_OR_SUBPLAN        | General Parameter        | Resource consumer group                                     |
| SHARES                 | CPU Resource Management   | Share in CPU sharing mode, must be an integer in [1,100]  |
| PARALLEL_SERVER_LIMIT   | Parallel Execution Resource Management | Maximum percentage of parallel resources that can be used by the consumer group, must be an integer in [0,100] |
| UTILIZATION_LIMIT   | CPU Resource Management   | Maximum CPU usage limit, must be an integer in [1,100]     |
| MAX_UTILIZATION_LIMIT    | CPU Resource Management   | Same as the field UTILIZATION_LIMIT                     |
| MGMT_P1                  | CPU Resource Management   | Same as the field SHARES                                   |
| SPA_LIMIT               | Memory Management         | Maximum percentage of user memory occupied by the consumer group (unit: %), public memory usage is not limited; must be an integer in [1,100] |
| SPA_LIMIT_RESERVED      | Memory Management         | Percentage of user memory reserved within the consumer group as private parts for each session (unit: %). The reserved user memory does not require application to the consumer group; must be an integer in [0,100] |
| SESSION_SPA_LIMIT       | Memory Management         | The maximum percentage of available memory occupied by the session in the resource group (unit: %). Must be an integer in [1,100] |
| EXECUTION_QUEUE_TIMEOUT | Execution Scheduling Management | Timeout duration for waiting in the queue when resources are insufficient, unit is seconds; must be an integer in [-1, 4294967295], -1 indicates indefinite timeout, NULL equals to the minimum value of -1 |
| CONCURRENCY_LIMIT       | Execution Scheduling Management  | Number of resource-intensive SQLs allowed to execute concurrently within the consumer group, must be an integer in [0, 2147483647], NULL indicates no limit |

Usage Instructions:

-  Optional parameters that are not assigned values or input as NULL will not be processed.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Optional parameters not configured
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2',
    10,
    20,
    30,
    40,
    50);
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN3',
    GROUP_OR_SUBPLAN => 'RESGROUP3',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20,
    UTILIZATION_LIMIT => 30,
    SHARES => 40,
    PARALLEL_SERVER_LIMIT => 50);
-- Only MGMT_P1 and MAX_UTILIZATION_LIMIT parameters configured
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4',
    MGMT_P1 => 10,
    MAX_UTILIZATION_LIMIT => 20);
-- Only PARALLEL_SERVER_LIMIT parameter configured
EXEC DBMS_RESOURCE_MANAGER.UPDATE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5',
    PARALLEL_SERVER_LIMIT => 50);
```

<span id="DELETE_CONSUMER_GROUP_MAPPING" name="DELETE_CONSUMER_GROUP_MAPPING" class="yaslink"></span>

## DELETE\_CONSUMER\_GROUP\_MAPPING

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE IN VARCHAR(64),
    VALUE     IN VARCHAR(64));
```

This program is used to delete the mapping of resource consumers to resource consumer groups. The mapping relationship between SYS user and SYS_GROUP group cannot be deleted.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| ATTRIBUTE      | Mapping attribute, only supports USER                   |
| VALUE          | The username in the mapping relationship, cannot be SYS user |

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    'USER',
    'SALES1');
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP_MAPPING(
    ATTRIBUTE => 'USER',
    VALUE => 'SALES2');
```

<span id="DELETE_PLAN_DIRECTIVE" name="DELETE_PLAN_DIRECTIVE" class="yaslink"></span>

## DELETE\_PLAN\_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN             IN VARCHAR(64),
    GROUP_OR_SUBPLAN IN VARCHAR(64));
```

This program is used to delete resource plan directives.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| PLAN                    | General Parameter        | Name of an existing resource plan                           |
| GROUP_OR_SUBPLAN | Resource consumer group                                  |

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN1',
    'RESGROUP1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN2',
    'RESGROUP2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    'RESPLAN3',
    'RESGROUP3');    
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN4',
    GROUP_OR_SUBPLAN => 'RESGROUP4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN => 'RESPLAN5',
    GROUP_OR_SUBPLAN => 'RESGROUP5');
```

<span id="DELETE_PLAN" name="DELETE_PLAN" class="yaslink"></span>

## DELETE\_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN (
    PLAN IN VARCHAR(64));
```

This program is used to delete a resource plan. It cannot delete an active resource plan or the built-in resource plans TOALL and SYS_GROUP.

|Parameter |Description |
| :--- | :--- |
| PLAN    | Name of an existing resource plan                           |

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    'RESPLAN3');
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN5');
EXEC DBMS_RESOURCE_MANAGER.DELETE_PLAN(
    PLAN => 'RESPLAN6');
```

<span id="DELETE_CONSUMER_GROUP" name="DELETE_CONSUMER_GROUP" class="yaslink"></span>

## DELETE\_CONSUMER\_GROUP

```plsql
DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP IN VARCHAR(64));
```

This program is used to delete a resource consumer group.

|Parameter |Description |
| :------------- | :--------- |
| CONSUMER_GROUP      | Resource consumer group |

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```plsql
-- Ignore parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP1');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP2');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    'RESGROUP3');
-- Specify parameter names
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP4');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP5');
EXEC DBMS_RESOURCE_MANAGER.DELETE_CONSUMER_GROUP(
    CONSUMER_GROUP => 'RESGROUP6');
```
