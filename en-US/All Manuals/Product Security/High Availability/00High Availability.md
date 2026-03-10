YashanDB's high availability is reflected in two aspects:

- Service High Availability: Reduce the time the system cannot provide services through various technical measures to ensure business continuity. For specific configuration methods and usage steps, please refer to [High Availability](../../高可用/YashanDB高可用概述).

    - Select an appropriate [Disaster Recovery Mechanism](Disaster Recovery Mechanism) based on actual needs, such as primary/standby deployment, cascade standby deployment, etc., and configure the corresponding leader election functionality. In case the primary database node is unavailable, automatically elect a new primary from the standby database nodes to achieve automatic switchover during a failure.

    - Adopt the [YashanDB for Cluster](YashanDB for Cluster) multi-instance architecture, so that when any instance fails, the system has failover and recovery capabilities, and service is not interrupted, with no business awareness during the failure period.

    - Perform timely [Data Synchronization](Data Synchronization) between data copies, which ensures consistency of data versions after failover/primary/standby switching and reduces the risk of data loss.

- [Data Reliability](../Data Reliability/00Data Reliability): The system supports data recovery capabilities after database data corruption.