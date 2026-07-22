YashanDB resource management configures the allocation rules of physical resources (CPU, memory, etc.) to meet the resource demands of different users or programs:

- [CPU Resource Management](#cpu) is applicable for Standalone Deployment (non-cascade standby) and ISC Distributed Cluster Deployment, compatible with LINUX and ARM platforms, but not suitable for Docker containers.

- [Memory Resource Management](#mem) is applicable for Standalone Deployment (non-cascade standby) and ISC Distributed Cluster Deployment.

- [Execution Scheduling Management](#execution) is applicable for ISC Distributed Cluster Deployment.

<span id="cpu" name="cpu"></span>

## CPU Resource Management

The CPU resource management of YashanDB is used to ensure stable operation of the database, protect the usage of core users, handle urgent tasks, and maximize overall CPU resource utilization.

The CPU resource management functionality is node-specific. For example, setting a user's CPU resource cap to 20% means that this user can use up to 20% of the CPU on each node in the environment. The CPU resource cap may fluctuate, with fluctuations within a maximum of +3% being considered normal.

Resource plan directives for CPU resource management are primarily constrained by the parameters **SHARES**, **UTILIZATION_LIMIT**, and **MGMT_P1**.

### SHARES

The usage share in CPU shared mode must be an integer between [1,100]. When CPU resources are insufficient, a larger value will result in obtaining more CPU resources. However, the specific allocation of CPU resources will be determined based on calculations related to the shares of other resource usage groups.

To ensure that the built-in resource usage group SYS_GROUP has enough resources available, the system will dynamically adjust according to user input when the shared resources (MGMT_P1) of this group are either low or high. If SYS_GROUP's MGMT_P1 global ratio is below 40% or above 60%, the system will adjust SYS_GROUP's MGMT_P1 to equal the total of MGMT_P1 from other resource usage groups. In this case, the MGMT_P1 value for SYS_GROUP may exceed 100 (this can be verified by checking the MGMT_P1 field in the view DBA_RSRC_PLAN_DIRECTIVES; the calculation formula for the global ratio is: SYS_GROUP's MGMT_P1 / the total MGMT_P1 of all resource usage groups).

### UTILIZATION_LIMIT

The maximum possible utilization rate of CPU must be an integer between [1,100]. Regardless of the system's CPU resources, the CPU resources allocated to the resource usage group will not exceed this limit. The formula for calculating the maximum CPU utilization is `UTILIZATION_LIMIT * number of CPUs`. For example, if a user-mapped resource usage group sets UTILIZATION_LIMIT to 10 and the number of CPUs is 2, then this user can use a maximum of 20% of the CPU resources.

### MGMT_P1

The usage share in CPU shared mode has the same meaning as the **SHARES** parameter.

### MAX_UTILIZATION_LIMIT

The maximum possible utilization rate of CPU has the same meaning as the **UTILIZATION_LIMIT** parameter.

> **Note**:
>
> - When both MGMT_P1 and SHARES parameters are set, the **SHARES** parameter prevails.
> - When both MAX_UTILIZATION_LIMIT and UTILIZATION_LIMIT parameters are set, the **UTILIZATION_LIMIT** parameter prevails.

<span id="mem" name="mem"></span>

## Memory Resource Management

YashanDB user memory resource management mainly focuses on allocating and managing the memory that a user can exclusively occupy within a certain time (i.e., SPA, for details refer to [Memory Architecture](../../Product Concepts/Instance Architecture/Database Memory)). Common buffers or other memory areas without user attributes are not managed.

Resource plan directives for user memory resource management primarily constrain session and resource usage group memory usage through the parameters **SPA_LIMIT**, **SESSION_SPA_LIMIT**, and **SPA_LIMIT_RESERVED**.

### SPA_LIMIT

The `SPA_LIMIT` in the plan directive indicates the maximum proportion of user memory that the corresponding resource usage group can utilize across the entire system, excluding common buffers and other memory. In multi-instance deployments (e.g., ISC Distributed Cluster Deployment), `SPA_LIMIT` refers to the proportion of local instance (or node) internal SPA memory. For example, if the CN node and DN node have different memory configurations, the upper limit of SPA memory usable by the resource usage group on the corresponding node is calculated based on the `SPA_LIMIT` proportion, meaning each node's final memory size may not be the same.

### SESSION_SPA_LIMIT

While sessions under a resource usage group are constrained by the overall SPA memory cap ratio of the resource usage group (`SPA_LIMIT`), they are also affected by the individual session's `SESSION_SPA_LIMIT`. `SESSION_SPA_LIMIT` indicates the proportion of the total SPA memory limit for the resource usage group that a session (corresponding to a user belonging to that resource usage group) can use. For example, if the `SPA_LIMIT` and `SESSION_SPA_LIMIT` of resource usage group HIGH_GROUP are set at 50 and 10 respectively, then the session can use up to 5% of the total SPA memory (i.e., 50% * 10%).

During the session's execution, it will be constrained by the session SPA memory cap, while multiple concurrent sessions will also be limited by the resource usage group's SPA memory cap.

### SPA_LIMIT_RESERVED

In scenarios with high concurrency and performance sensitivity, centralized management of SPA memory cannot avoid performance loss. The `SPA_LIMIT_RESERVED` in the plan directive allows the resource usage group to reserve a certain proportion of the SPA memory quota, which is distributed evenly among sessions in advance, enabling sessions to operate without needing to request or release resources from the resource usage group as long as they remain within the reserved quota, thus achieving better performance.

Due to differences in the number of active sessions under various deployment configurations, the basis for reserving quotas is also different:

-  In Standalone Deployment, the reserved quota is allocated according to the value of the `MAX_SESSIONS` configuration parameter;
- In ISC Distributed Cluster Deployment, the reserved quota is allocated according to the value of the `MAX_WORKERS` configuration parameter. If the value of `MAX_WORKERS` is 0, it is calculated as twice the number of CPU cores on the corresponding server.

<span id="execution" name="execution"></span>

## Execution Scheduling Management

YashanDB execution scheduling management coordinates global execution resources, queuing, and scheduling to execute tasks in a reasonable and orderly manner, maximizing resource utilization and execution efficiency.
- Global execution resource coordination: Resources are pre-occupied at each node through global assessment before execution to avoid ineffective contention.
- Queuing scheduling mechanism: The number of sessions executing resource-intensive SQL operations concurrently is controlled to achieve orderly resource usage.

Resource plan directives for execution scheduling management are primarily managed through the parameters **PARALLEL_SERVER_LIMIT**, **EXECUTION_QUEUE_TIMEOUT**, and **CONCURRENCY_LIMIT**.

### PARALLEL_SERVER_LIMIT

YashanDB parallel execution resource management pre-allocates parallel threads prior to execution, effectively reducing the likelihood of errors due to insufficient parallel threads under high concurrency. A certain number of parallel threads are allocated to each resource usage group, and if adequate parallel threads cannot be obtained during execution, the system will wait and retry until it acquires the required thread resources.

The maximum percentage of parallel resources for a resource usage group must be an integer between [0,100]. The available number of parallel threads for each resource usage group can be calculated as `MAX_PARALLEL_WORKERS * (PARALLEL_SERVER_LIMIT / 100)`.

### EXECUTION_QUEUE_TIMEOUT

In ISC Distributed Cluster Deployment scenarios, to improve success rates, resource pre-occupation occurs prior to execution, while the system supports retry handling in case of resource allocation conflicts. `EXECUTION_QUEUE_TIMEOUT` represents the maximum time allowed for retries and waiting during execution, excluding execution time and retry execution time; exceeding this time will result in an execution failure error.

### CONCURRENCY_LIMIT

In OLAP scenarios where numerous resource-intensive operations are performed simultaneously, the performance is often suboptimal due to intense resource contention. By configuring the `CONCURRENCY_LIMIT` parameter, the maximum number of sessions allowed to perform resource-intensive SQL operations simultaneously within the same resource usage group can be controlled. Once the threshold is reached, such sessions must wait in a queue, and if the waiting time exceeds the `EXECUTION_QUEUE_TIMEOUT`, an error will be prompted, terminating the session.

The types of resource-intensive SQL operations subject to queuing scheduling control can be configured in the `RSRC_QUEUE_OPERS` parameter; for more details, please refer to [Configuration Parameters](../../Reference Manual/Configuration Parameters).