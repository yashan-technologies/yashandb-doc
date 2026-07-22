Dynamic views are system-provided views that start with V$, GV$, or DV$. They are used to provide real-time data related to database operations, especially performance-related data. Users can manage and optimize the system by querying these views.

- V$ Views: Local dynamic views that query data from the currently active instance node. They perform consistently across all deployment types.

- GV$ Views: Global dynamic views, where GROUP_ID, GROUP_NODE_ID, and INST_ID are common fields used to distinguish different instances. The data queried in GV$ views differs across different deployment types:
    - In YAC/Distributed Cluster Deployment, GV$ views query and aggregate data from all instances. INST_ID is used to distinguish between different instances, while GROUP_ID and GROUP_NODE_ID are always 0.

    - In ISC Distributed Cluster Deployment, the CN node's GV$ view queries the aggregated results from all nodes, while other nodes' GV$ views are equivalent to V$ views. The GROUP_ID and GROUP_NODE_ID fields are used to distinguish different node groups and nodes, while INST_ID is always 1.

    - In Standalone Deployment, the GV$ view is equivalent to the V$ view, with GROUP_ID and GROUP_NODE_ID always 0, and INST_ID always 1.

- DV$ Views: Distributed dynamic views that only exist in ISC Distributed Cluster Deployment, querying the aggregated results from all nodes' data.

> **Note**: 
> 
> The GV$ view is semantically consistent with the DV$ view, but there are slight differences in view definitions and data aggregation methods.
>
> GV$ views are not allowed to be queried in conjunction with DV$ views.
>
> In ISC Distributed Cluster Deployment, GV$ views cannot be temporarily related to user tables.