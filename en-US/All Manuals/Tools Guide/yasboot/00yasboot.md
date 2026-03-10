*yasboot* is the operational management tool that comes with YashanDB, used for various remote operational management tasks on YashanDB in a database cluster environment, such as:

- Installation and Deployment
- Offline Upgrade
- Instance Start and Stop
- Primary/Standby Switch
- AC Discovery
- Metrics Inspection
- Data Redistribution
- sys Account Password Setting
- Parameter Setting
- Process Daemon Management
- Log Collection
- yasql Tool Integration
- IP Change
- Schedule Management

The following prerequisites must be met to use the *yasboot* tool:

* The [toml configuration file](Configuration Files/00Configuration Files) has been configured, including the server configuration file and the cluster configuration file. The command yasboot package config gen will generate the corresponding toml files during product installation.
* Communication ports between nodes are open.
* It is recommended to execute *yasboot* as a non-root user.
* *yasboot* restricts non-local usage scenarios and must be executed under the same user on the same server where the database is deployed.

Precautions for using *yasboot*:

* Terminating the *yasboot* process will not affect the commands successfully issued to yasom.