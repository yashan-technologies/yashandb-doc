The DBMS_RESOURCE_MANAGER package provides a set of [resource management](../../../Database Administration/Resource Management/00Resource Management) stored procedures/functions for creating and deleting resource consumer groups, resource mappings, and related operations.

> **Note**:
>
> - When calling all subprograms in the DBMS_RESOURCE_MANAGER advanced package, you must connect to the database as the SYS user; otherwise, an error will occur.
> - This advanced package is not applicable to YAC/Distributed Cluster Deployment.
> - In ISC Distributed Cluster Deployment, all procedures/functions in the advanced package DBMS_RESOURCE_MANAGER are only allowed to be called one by one and can only be operated on the CN node.

## CREATE_CONSUMER_GROUP

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

## CREATE_PLAN

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
| PLAN | Resource plan name, name must be unique and comply with YashanDB's [object naming conventions](../../SQL Reference Manual/Basic SQL Elements/Identifiers), reserved keywords are allowed, but do not meet [the general rules for double quotes](../../SQL Reference Manual/Basic SQL Elements/Double Quotes)  |
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

## CREATE_PLAN_DIRECTIVE

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
| PLAN                    | General Parameter        | Resource plan name                                          |
| GROUP_OR_SUBPLAN        | General Parameter        | Resource consumer group                                     |
| MGMT_P1                 | CPU Resource Management   | Share in CPU sharing mode, must be an integer in [1,100], NULL indicates a minimum value of 1  |
| MAX_UTILIZATION_LIMIT   | CPU Resource Management   | Maximum CPU usage limit, must be an integer in [1,100], NULL indicates a maximum value of 100  |
| UTILIZATION_LIMIT       | CPU Resource Management   | Same as the field MAX_UTILIZATION_LIMIT                     |
| SHARES                  | CPU Resource Management   | Same as the field MGMT_P1                                   |
| PARALLEL_SERVER_LIMIT   | Parallel Execution Resource Management | Maximum percentage of parallel resources that can be used by the consumer group, must be an integer in [0,100], NULL indicates a maximum value of 100 |
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

## SET_CONSUMER_GROUP_MAPPING

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

## UPDATE_PLAN_DIRECTIVE

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
| PLAN                    | General Parameter        | Resource plan name                                          |
| GROUP_OR_SUBPLAN        | General Parameter        | Resource consumer group                                     |
| MGMT_P1                 | CPU Resource Management   | Share in CPU sharing mode, must be an integer in [1,100]  |
| MAX_UTILIZATION_LIMIT   | CPU Resource Management   | Maximum CPU usage limit, must be an integer in [1,100]     |
| UTILIZATION_LIMIT       | CPU Resource Management   | Same as the field MAX_UTILIZATION_LIMIT                     |
| SHARES                  | CPU Resource Management   | Same as the field MGMT_P1                                   |
| PARALLEL_SERVER_LIMIT   | Parallel Execution Resource Management | Maximum percentage of parallel resources that can be used by the consumer group, must be an integer in [0,100] |
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

## DELETE_CONSUMER_GROUP_MAPPING

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

## DELETE_PLAN_DIRECTIVE

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN_DIRECTIVE(
    PLAN             IN VARCHAR(64),
    GROUP_OR_SUBPLAN IN VARCHAR(64));
```

This program is used to delete resource plan directives.

|Parameter |Description |
| :-------- | :----------------------------------------------------------- |
| PLAN           | Resource plan name                                       |
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

## DELETE_PLAN

```plsql
DBMS_RESOURCE_MANAGER.DELETE_PLAN (
    PLAN IN VARCHAR(64));
```

This program is used to delete a resource plan. It cannot delete an active resource plan or the built-in resource plans TOALL and SYS_GROUP.

|Parameter |Description |
| :--- | :--- |
| PLAN | Resource plan name |

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

## DELETE_CONSUMER_GROUP

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
