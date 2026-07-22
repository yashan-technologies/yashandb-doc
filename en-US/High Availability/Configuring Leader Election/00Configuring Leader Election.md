To ensure business continuity, YashanDB supports the configuration of leader election in a high availability environment. When the primary database cannot serve externally due to exceptions, the system selects a new primary database from the standby database based on the corresponding mechanism and automatically executes the primary/standby switch, reducing operational complexity.

According to different high availability configurations, leader election can be divided into:

- [Leader Election for One Primary and Multi-Standby](./Configuring Leader Election for One Primary and Multi-Standby): Suitable for standalone one-primary/multi-standby deployments and ISC Distributed Cluster Deployment where the node scale within the DN group is one-primary/multi-standby.

- [*yasom* Election](./Configuring yasom Election): Suitable for Standalone One-Primary/One-Standby Deployment, Primary-Standby YAC Deployment, or ISC Distributed Cluster Deployment where nodes within the DN group are configured as one-primary/one-standby.
