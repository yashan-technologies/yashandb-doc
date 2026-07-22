## What is IO Fencing

A YAC/distributed cluster database consists of shared database files that is persisted on storage device(s) and multiple peer, read-write member servers. When certain member servers encounter exceptions (such as private network isolation, storage network I/O exceptions, etc.), it is necessary to prevent the already evicted illegal member servers from continuing to modify the data file before updating the cluster member relationships based on the results of the voting arbitration. This process is called I/O Fencing.

When deploying YAC/distributed cluster, *yasboot* will automatically select the optimal fence type. If needed, users can cautiously modify the RTO requirements according to the hardware configuration of the deployment environment and business scenarios after the cluster is deployed. Before making modifications, please read the documentation of the relevant fence types carefully and execute the required hardware capability testing scripts to confirm that the hardware capabilities meet the requirements.

## IO Fencing Methods Supported by YCS

- [In-Transit I/O Protection Algorithm](./In-Transit IO Protection Algorithm): Does not rely on hardware capabilities of storage devices, has strong compatibility, but slightly lower safety and RTO.

- [Reservation-based IO Fencing](./Reservation-based IO Fencing): Requires storage devices to support the corresponding protocols, offering the best security and RTO. Supports both SCSI Persistent Reservation and NVMe Reservation protocols.
