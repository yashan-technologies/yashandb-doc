YashanDB resource management configures physical resource (CPU, memory, etc.) allocation rules to meet different users or programs' resource requirements. Through resource management, the following goals can be achieved:

- Provide resource isolation, ensuring that users can access their allocated resources even in extreme scenarios, unaffected by other users.
- Maximize overall resource utilization while ensuring the database operates stably.
- Allow configuration and switching between different resource usage plans without needing a restart, such as switching plans suitable for daytime and nighttime.
- Real-time monitoring of physical resource allocation and usage.

## Core Concepts

### Resource Usage Group

A resource usage group consists of resource users who share a resource allocation instruction within the same group.

The system includes the following default resource usage groups:

- SYS_GROUP: Includes the system user sys and system processes.
- DEFAULT_CONSUMER_GROUP: Resource users without a mapping relationship are defaulted to this group.

When creating a session, YashanDB automatically assigns users to the appropriate resource usage group based on their resource mapping relationship. Database administrators can adjust a user's new session's resource usage group by modifying the user's resource mapping relationship.

### Resource Mapping

Resource mapping refers to adding resource users to resource usage groups, where resource users are categorized by user (USER).

The relationship between resource usage groups and resource users is one-to-many, meaning a resource user can only join one resource usage group, but one resource usage group can contain multiple resource users.

### Resource Plan

A resource plan is an allocation plan for the physical resources (e.g., CPU) of each node in the environment, specifying how resources are allocated to resource usage groups by activating specific resource plans.

The system includes the following default resource plans:

- TOALL: A primary resource plan, which contains the resource plan instructions for the sub-plans SYS_GROUP and DEFAULT_CONSUMER_GROUP.
- SYS_GROUP: A secondary resource plan, which is a sub-plan of TOALL and contains resource plan instructions related to the system user sys and system processes.

Switching between different resource plans allows for changing the corresponding resource plan instructions for all resource usage groups.

### Resource Plan Instruction

Resource plan instructions describe the allocation rules for physical resources assigned to resource usage groups under different plans.

There is a one-to-many relationship between resource plans and resource plan instructions, meaning each resource group can only specify one resource plan instruction within a resource plan.

## Resource Types

YashanDB resource management configures the allocation rules of physical resources (CPU, memory, etc.) to meet the resource demands of different users or programs:

- [CPU Resource Management](#cpu) is applicable for all deployment forms (only for the compute cluster in Distributed Cluster Deployment), supports Linux and ARM platforms, and is not applicable to Docker environments.

- [Memory Resource Management](#mem) is applicable for all deployment forms (only for the compute cluster in Distributed Cluster Deployment).

- [Execution Scheduling Management](#execution) is applicable for ISC Distributed Cluster Deployment.

<span id="cpu" name="cpu"></span>

### CPU Resource Management

The CPU resource management of YashanDB is used to ensure stable operation of the database, protect the usage of core users, handle urgent tasks, and maximize overall CPU resource utilization.

The CPU resource management functionality is node-specific. For example, setting a user's CPU resource cap to 20% means that this user can use up to 20% of the CPU on each node in the environment. The CPU resource cap may fluctuate, with fluctuations within a maximum of +3% being considered normal.

Resource plan directives for CPU resource management are primarily constrained by the parameters **SHARES** and **UTILIZATION_LIMIT**.

#### SHARES

The usage share in CPU shared mode must be an integer between [1,100]. When CPU resources are insufficient, a larger value will result in obtaining more CPU resources. However, the specific allocation of CPU resources will be determined based on calculations related to the shares of other resource usage groups.

To ensure that the built-in resource usage group SYS_GROUP has enough resources available, the system will dynamically adjust according to user input when the shared resources (SHARES) of this group are either low or high. If SYS_GROUP's SHARES global ratio is below 40% or above 60%, the system will adjust SYS_GROUP's SHARES to equal the total of SHARES from other resource usage groups. In this case, the SHARES value for SYS_GROUP may exceed 100 (this can be verified by checking the SHARES field in the view DBA_RSRC_PLAN_DIRECTIVES; the calculation formula for the global ratio is: SYS_GROUP's SHARES / the total SHARES of all resource usage groups).

#### UTILIZATION_LIMIT

The maximum possible utilization rate of CPU must be an integer between [1,100]. Regardless of the system's CPU resources, the CPU resources allocated to the resource usage group will not exceed this limit. The formula for calculating the maximum CPU utilization is `UTILIZATION_LIMIT * number of CPUs`. For example, if a user-mapped resource usage group sets UTILIZATION_LIMIT to 10 and the number of CPUs is 2, then this user can use a maximum of 20% of the CPU resources.

<span id="mem" name="mem"></span>

### Memory Resource Management

YashanDB user memory resource management mainly focuses on allocating and managing the memory that a user can exclusively occupy within a certain time (i.e., SPA, for details refer to [Memory Architecture](../../Product Concepts/Instance Architecture/Database Memory)). Common buffers or other memory areas without user attributes are not managed.

Resource plan directives for user memory resource management primarily constrain session and resource usage group memory usage through the parameters **SPA_LIMIT**, **SESSION_SPA_LIMIT**, and **SPA_LIMIT_RESERVED**.

#### SPA_LIMIT

The `SPA_LIMIT` in the plan directive indicates the maximum proportion of user memory that the corresponding resource usage group can utilize across the entire system, excluding common buffers and other memory. In multi-instance deployments (e.g., ISC Distributed Cluster Deployment), `SPA_LIMIT` refers to the proportion of local instance (or node) internal SPA memory. For example, if the CN node and DN node have different memory configurations, the upper limit of SPA memory usable by the resource usage group on the corresponding node is calculated based on the `SPA_LIMIT` proportion, meaning each node's final memory size may not be the same.

#### SESSION_SPA_LIMIT

While sessions under a resource usage group are constrained by the overall SPA memory cap ratio of the resource usage group (`SPA_LIMIT`), they are also affected by the individual session's `SESSION_SPA_LIMIT`. `SESSION_SPA_LIMIT` indicates the proportion of the total SPA memory limit for the resource usage group that a session (corresponding to a user belonging to that resource usage group) can use. For example, if the `SPA_LIMIT` and `SESSION_SPA_LIMIT` of resource usage group HIGH_GROUP are set at 50 and 10 respectively, then the session can use up to 5% of the total SPA memory (i.e., 50% * 10%).

During the session's execution, it will be constrained by the session SPA memory cap, while multiple concurrent sessions will also be limited by the resource usage group's SPA memory cap.

#### SPA_LIMIT_RESERVED

In scenarios with high concurrency and performance sensitivity, centralized management of SPA memory cannot avoid performance loss. The `SPA_LIMIT_RESERVED` in the plan directive allows the resource usage group to reserve a certain proportion of the SPA memory quota, which is distributed evenly among sessions in advance, enabling sessions to operate without needing to request or release resources from the resource usage group as long as they remain within the reserved quota, thus achieving better performance.

Due to differences in the number of active sessions under various deployment configurations, the basis for reserving quotas is also different:

-  In Standalone Deployment, YAC Deployment, or Distributed Cluster Deployment, the reserved quota is allocated according to the value of the `MAX_SESSIONS` configuration parameter;
- In ISC Distributed Cluster Deployment, the reserved quota is allocated according to the value of the `MAX_WORKERS` configuration parameter. If the value of `MAX_WORKERS` is 0, it is calculated as twice the number of CPU cores on the corresponding server.

<span id="execution" name="execution"></span>

### Execution Scheduling Management

YashanDB execution scheduling management coordinates global execution resources, queuing, and scheduling to execute tasks in a reasonable and orderly manner, maximizing resource utilization and execution efficiency.
- Global execution resource coordination: Resources are pre-occupied at each node through global assessment before execution to avoid ineffective contention.
- Queuing scheduling mechanism: The number of sessions executing resource-intensive SQL operations concurrently is controlled to achieve orderly resource usage.

Resource plan directives for execution scheduling management are primarily managed through the parameters **PARALLEL_SERVER_LIMIT**, **EXECUTION_QUEUE_TIMEOUT**, and **CONCURRENCY_LIMIT**.

#### PARALLEL_SERVER_LIMIT

YashanDB parallel execution resource management pre-allocates parallel threads prior to execution, effectively reducing the likelihood of errors due to insufficient parallel threads under high concurrency. A certain number of parallel threads are allocated to each resource usage group, and if adequate parallel threads cannot be obtained during execution, the system will wait and retry until it acquires the required thread resources.

The maximum percentage of parallel resources for a resource usage group must be an integer between [0,100]. The available number of parallel threads for each resource usage group can be calculated as `MAX_PARALLEL_WORKERS * (PARALLEL_SERVER_LIMIT / 100)`.

#### EXECUTION_QUEUE_TIMEOUT

In ISC Distributed Cluster Deployment scenarios, to improve success rates, resource pre-occupation occurs prior to execution, while the system supports retry handling in case of resource allocation conflicts. `EXECUTION_QUEUE_TIMEOUT` represents the maximum time allowed for retries and waiting during execution, excluding execution time and retry execution time; exceeding this time will result in an execution failure error.

#### CONCURRENCY_LIMIT

In OLAP scenarios where numerous resource-intensive operations are performed simultaneously, the performance is often suboptimal due to intense resource contention. By configuring the `CONCURRENCY_LIMIT` parameter, the maximum number of sessions allowed to perform resource-intensive SQL operations simultaneously within the same resource usage group can be controlled. Once the threshold is reached, such sessions must wait in a queue, and if the waiting time exceeds the `EXECUTION_QUEUE_TIMEOUT`, an error will be prompted, terminating the session.

Resource-intensive SQL types subject to queuing scheduling control can be configured through the RSRC_QUEUE_OPERS parameter.

## Configuring Resource Management

YashanDB resource management provides physical resource configuration capabilities through the built-in advanced package [DBMS_RESOURCE_MANAGER](../../Development Guide/PL Reference Manual/Built-in Advanced PL Packages/DBMS_RESOURCE_MANAGER) and related configuration parameters; for more details, please refer to [Configuring Resource Management](Configuring Resource Management).

### RSRC_MODE

YashanDB controls whether resource management capabilities are enabled for certain physical resources through the RSRC_MODE parameter. The optional values are as follows:

- NONE: Default value, indicates that resource management for any resource type is not enabled.

- CPU: Indicates that CPU resource isolation and management are enabled.

- MEM: Indicates that user memory resource isolation and management are enabled.

- ALL: Indicates that both CPU and user memory resource isolation and management are enabled.

Changes to this configuration parameter require a restart of the instance to take effect.

### RESOURCE_MANAGER_PLAN

YashanDB activates a specific resource plan through the RESOURCE_MANAGER_PLAN parameter, which can only be specified as the name of a custom-created resource plan.

### RSRC_QUEUE_OPERS

YashanDB uses the RSRC_QUEUE_OPERS parameter to specify the types of resource-intensive SQL. Sessions executing these SQL statements will be subject to scheduling control, and when the number of concurrently executing corresponding operations exceeds the CONCURRENCY_LIMIT field value in the resource plan directive, they need to wait in queue.

- NONE: Default value, indicates that no operations enable the queuing and scheduling mechanism.

- COL_QUERY: LSC table query operations.

- ALL: Optional operations that enable the queuing and scheduling mechanism.

## Resource Management in CDB

When YashanDB is deployed as a CDB (configuration parameter ENABLE_PLUGGABLE_DATABASE=TRUE), resource management supports two-level hierarchical management at CDB and PDB levels.

### Resource Management Methods

- [CDB-level resource management](./Configuring CDB Global Resource Management): 

    Mainly used to control the resource allocation strategy of the entire CDB, it defines overall scheduling rules and allocation frameworks for key system resources such as CPU and memory at the CDB level. Specific resource quotas can be allocated to various PDBs through resource plan directives, forming a parent-child relationship between CDB resource plans and their directives. At the CDB level, DBAs can allocate resources to specific PDBs through three methods: dedicated CDB resource plan directives, configuring CDB resource plan templates for PDBs, or using CDB default resource plan directives.The priority of various directives taking effect on PDBs is CDB resource plan directive (CDB_PLAN_DIRECTIVE) > CDB resource plan template (CDB_PROFILE_DIRECTIVE) > CDB default resource plan directive (CDB_DEFAULT_DIRECTIVE).



    - Dedicated CDB resource plan directives: Specify dedicated directives for individual PDBs, creating personalized directives through the CREATE_CDB_PLAN_DIRECTIVE procedure to achieve precise resource control for specific PDBs.

    - CDB resource template directives: Batch specify unified directive templates for multiple PDBs, creating directive templates through CREATE_CDB_PROFILE_DIRECTIVE and completing template-to-PDB mapping association using SET_PDB_PROFILE, suitable for PDB groups with similar resource requirements.

    - CDB default resource plan directives: Provide default resource configuration for PDBs that have not been assigned any CDB resource plan directives. Default resource plan directives can be adjusted through UPDATE_CDB_DEFAULT_DIRECTIVE.

- PDB-level resource management: 
    
    Used for fine-grained control of resource allocation strategies within PDBs. For specific operations.

### CDB Global Resource Types

|Resource Types |Parameter |Description |
| :------------- | :---------------------- |:----------------------------------------------------------- |
| CPU | SHARES                | Allocate CPU resources proportionally among PDBs. |
|  | UTILIZATION_LIMIT     | Control the CPU resources that each PDB can use through upper limit ratio values, preventing any PDB from excessively occupying resources and affecting the normal usage of other PDBs. <br />The total amount of CPU resources defaults to all CPU resources of the server where the database is located. |
| IOPS | SHARES | Allocate IOPS resources proportionally among PDBs. <br /> The upper limit base values for maximum IO operations per second and maximum read/write bytes per second allowed can be configured in the CDB root through MAX_IOPS and MAX_BPS parameters.|
|  | UTILIZATION_LIMIT | Control the IOPS resources that each PDB can use through upper limit ratio values, preventing any PDB from excessively occupying resources and affecting the normal usage of other PDBs.<br /> The upper limit base values for maximum IO operations per second and maximum read/write bytes per second allowed can be configured in the CDB root through MAX_IOPS and MAX_BPS parameters. |
| Parallel Execution Resource | PARALLEL_SERVER_LIMIT | Used for compatibility support, providing restriction management for parallel processing resources. |
| Memory | MEMORY_LIMIT          | Control the proportion of total physical memory that each PDB can use through upper limit ratio values. <br />The total amount of physical memory that the entire CDB can use can be specified through the CDB_MAX_MEMORY_SIZE parameter in the CDB root. If this parameter is configured to 0 (default value), it indicates that the upper limit is 80% of the total physical memory of the server where the database is located. |
|  | MEMORY_MIN            | Ensure that PDBs receive necessary memory resources through lower limit ratio values, avoiding issues where PDBs cannot start normally due to improper configuration. |
