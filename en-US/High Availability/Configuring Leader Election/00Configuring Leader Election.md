To ensure business continuity, YashanDB supports the configuration of leader election in a high availability environment. When the primary database cannot serve externally due to exceptions, the system selects a new primary database from the standby database based on the corresponding mechanism and automatically executes the primary/standby switch, reducing operational complexity.

According to different high availability configurations, leader election can be divided into:

- [One-primary/multi-standby leader election](./Configuring Leader Election for One Primary and Multi-Standby): Suitable for standalone one-primary/multi-standby deployments and ISC Distributed Cluster Deployment where the node scale within the DN group is one-primary/multi-standby.

- [One-primary/one-standby yasom election](./Configuring yasom Election for One Primary and One Standby): Suitable for standalone one-primary/one-standby deployments, primary/standby cluster deployments with a scale of one-primary/one-standby, and ISC Distributed Cluster Deployment where the node scale within the DN group is one-primary/one-standby.
