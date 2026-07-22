In a standalone deployment with one primary and three standbys (non-cascaded standbys), *yasom* high availability (HA), also known as automatic leader election for *yasom*, has been implemented.

## Feature Overview

After enabling the *yasom* automatic leader election feature, standby *yasom* instances automatically monitor the health of the primary *yasom*. Once a failure is detected, leader election is triggered, enabling automatic *yasom* role switching without manual intervention.

The *yasom* automatic leader election feature depends on YCM (YashanDB Cloud Manager) for implementation and proper operation:

- Remote storage of critical metadata: Uses the YCM backend database to store the heartbeat table that records primary-standby *yasom* heartbeat information and automatic leader election related data.

- Fault isolation: Target database failures and *yasom* automatic leader election metadata failures are completely isolated.

### Heartbeat Detection and Leader Election

All primary and standby *yasom* instances share a heartbeat table (named `om_heartbeat`), which stores only the heartbeat record of the current primary *yasom*, including the term number (`term`) and heartbeat count (`heartbeat_cnt`).

The heartbeat table fields are described as follows:

| Field         | Description                                                                          |
|---------------|--------------------------------------------------------------------------------------|
| om_uuid       | UUID of the primary or standby *yasom*                                               |
| om_addr       | Listening address of the primary *yasom*                                             |
| agent_addr       | The *yasagent* listening address on the primary *yasom*  machine                     |
| hostid        | ID of the server where the primary *yasom* resides                                   |
| id            | Primary key                                                                          |
| term          | Term number                                                                          |
| heartbeat_cnt | Heartbeat count of the primary *yasom*                                               |
| om_data       | *yasom* metadata, used for data synchronization after *yasom* primary-standby switch |
| last_heartbeat        | The last heartbeat timestamp of the primary *yasom*                                  |

- **Standby *yasom* heartbeat detection**: Standby *yasom* queries the `heartbeat_cnt` in the heartbeat table at the heartbeat interval (default: 1 second). If the count keeps increasing, the primary *yasom* is healthy; if the count remains unchanged within the timeout period (default: 5 seconds), the primary *yasom* is considered failed and leader election is triggered.

- **Leader election competition**: When multiple standby *yasom* instances initiate leader election simultaneously, each reads the current `term` and `heartbeat_cnt`, then attempts an atomic update with conditional checks. Since the database guarantees that only one update can match the conditions and take effect at a time, only one standby *yasom* can successfully become the primary. After success, `term` is incremented by 1 and the `om_uuid` and `listenAddr` fields are updated with the new primary *yasom* information.

- **Metadata synchronization**: The primary *yasom* periodically exports local SQLite metadata to SQL statements and writes them to the `om_data` field of the heartbeat table. After a new primary is elected, it automatically restores metadata from this field.

### Primary *yasom* Keep-Alive and Self-Termination

- **Keep-alive**: The primary *yasom* updates the heartbeat table at the heartbeat interval, incrementing `heartbeat_cnt` by 1 to indicate it is alive.

- **Self-termination**: If the primary *yasom* detects that `term` has been modified by another instance during heartbeat update (indicating a new primary exists), or if the update times out and fails, it proactively exits the process. After exiting, it is automatically restarted by the system process monitoring tool (such as monit) and automatically rejoins as a standby to resume heartbeat detection.

### State Transition

The state transition during the entire automatic leader election process is as follows:

```text
Standby (standby yasom) --election success--> Active (primary yasom) --keep-alive failure/term change--> Suicide (self-termination) --> restart --> Standby (standby yasom)
```

## Specifications and Constraints

| Constraint Item | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Database Version | v23.4.14.100 or later                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Deployment Mode | Standalone deployment with one primary and three standbys (non-cascaded standbys)                                                                                                                                                                                                                                                                                                                                                                                                                         |
| YCM Dependency | The monitoring and management platform (YCM) for the target YashanDB must be deployed simultaneously, and YCM must be in a one-primary-two-standby configuration or higher. *yasom* automatic leader election functions properly only when YCM is running normally. A connection user must be created in the YCM backend YashanDB database and granted `create session`, `create table`, and `create sequence` privileges.                                                                                |
| Configuration Requirements | \* The heartbeat table must be created in the YCM backend YashanDB database<br/> \* The YCM backend YashanDB database must have automatic leader election enabled (HA_ELECTION_ENABLED = TRUE) and use maximum protection mode<br/> \* Primary and standby *yasom* processes must be deployed on different servers from the target YashanDB managed by *yasom*<br/> \* After enabling *yasom* automatic leader election, modifying the YCM backend YashanDB database connection parameters is not allowed |
| Configuration Change Constraints | To modify the heartbeat interval and timeout for *yasom* automatic leader election, you must disable the *yasom* automatic leader election feature first; the changes take effect immediately after modification                                                                                                                                                                                                                                                                                          |
| Process Recovery Mechanism | After the primary *yasom* self-terminates, it depends on the yasboot process monitoring tool (monit) for automatic restart. You can also manually start it by executing the `yasboot process yasom start -c yashandb` command on the *yasom* node                                                                                                                                                                                                                                                         |
| Feature Mutual Exclusivity | \* Mutually exclusive with auto-healing (yasboot process *yasom* repair); they cannot be enabled simultaneously <br/> \* Mutually exclusive with *yasom* recovery; the command yasboot process yasom recover cannot be executed after enabling automatic leader election                                                                                                                                                                                                                                  |
| Version Compatibility | If the database has been upgraded and standby *yasom* was deployed before the upgrade but the *yasom* high availability feature has never been used, you must execute yasboot process *yasom* clean -c yashandb to clean up the standby *yasom* and then rebuild the standby *yasom* after the upgrade.                                                                                                                                                                                                   |
## Configuring and Using *yasom* Automatic Leader Election

### Prerequisites

- Prepare at least 7 servers: 4 for deploying the standalone one-primary-three-standby business database, and 3 for running *yasom* processes (may overlap with YCM servers).

- Complete the [preparation work](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation.md) before YashanDB database deployment.

- Complete the deployment of YCM and ensure network connectivity with the business database.

- Obtain the connection information for the YCM backend database.

### Procedure

1. Deploy the business database:

   ```shell
   $ yasboot package se gen --cluster yashandb -u yashan -p yashan_password\
    --ip 192.168.1.1,192.168.1.2,192.168.1.3,192.168.1.4,192.168.1.5,192.168.1.6,192.168.1.7\
    --port 22  --install-path /data/yashan/yasdb_home --begin-port 1688 --node 4

   # Modify the yasom hostid in hosts.toml to host0005
   # [om]
   #   hostid = "host0005"

   # Modify yashandb.toml to delete host0005, host0006, and host0007 information,
   ```
   ```shell
   $ yasboot package install -t hosts.toml

   $ yasboot cluster deploy -t yashandb.toml -p sys_password
   ```

2. Log in to the planned other *yasom* devices 192.168.1.6 and 192.168.1.7 and execute the following commands to deploy standby *yasom*:

   ```shell
   $ yasboot process yasom recover -c yashandb -l 192.168.1.6:2775
   ```

   ```shell
   $ yasboot process yasom recover -c yashandb -l 192.168.1.7:2775

   # View yasom primary-standby status to get standby yasom IP and port
   $ yasboot process yasom status -c yashandb
   ```

3. Log in to the primary *yasom* device 192.168.1.5 and execute the following command to start the monit process:

   ```shell
   $ yasboot monit start -c yashandb
   ```

4. Prepare the YCM backend database:

   ```shell
   # Set maximum protection mode
   $ yasql sys/********@192.168.2.1:1601 -c "alter database set standby database to MAXIMIZE PROTECTION;"

   # Enable automatic leader election
   $ yasboot cluster config set --key HA_ELECTION_ENABLED --value TRUE -c yashandb

   # Log in to the YCM backend database with a DBA user and execute:
   SQL> create user yashan identified by yashan123;

   SQL> grant create table to yashan;

   SQL> grant create session to yashan;

   SQL> grant create sequence to yashan;
   ```

5. Log in to the primary *yasom* device 192.168.1.5 and execute the following command to configure *yasom* HA parameters:

   ```shell
   $ yasboot process yasom config set -c yashandb \
     --heartbeat-interval 1 \
     --heartbeat-timeout 5 \
     --heartbeat-db-url 192.168.2.1:16789,192.168.2.2:16789,192.168.2.3:16789 \
     --heartbeat-db-user yashan \
     --heartbeat-db-password yashan123
   # The heartbeat-db-url corresponds to the YCM backend database connection information

   Connecting to yasom...
     Updating yasom election config on all hosts...

   Election config update completed
     Updated config on  (192.168.1.5:1675)
     Updated config on  (192.168.1.6:2775)
     Updated config on  (192.168.1.7:2775)

   All hosts updated successfully
   ```

6. Enable *yasom* automatic leader election:

   ```shell
   $ yasboot process yasom election on -c yashandb

   Connecting to master yasom...
   Enabling election on all yasom...

   Election enable result:
     host0005 (192.168.1.5:1675)
     host0006 (192.168.1.6:2775)
     host0007 (192.168.1.7:2775)

   Election enabled successfully on all yasom
   ```

7. (Optional) Query the automatic leader election configuration:

   ```shell
   $ yasboot process yasom config show -c yashandb

   === Election Configuration ===

   Host: host0005 (192.168.1.5)
     Om Addr: 192.168.1.5:1675
     Role: primary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan

   Host: host0006 (192.168.1.6)
     Om Addr: 192.168.1.6:2775
     Role: secondary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan

   Host: host0007 (192.168.1.7)
     Om Addr: 192.168.1.7:2775
     Role: secondary
     Enabled: true
     Heartbeat Interval: 1 seconds
     Heartbeat Timeout: 5 seconds
     DB URL: 192.168.2.1:16789
     DB User: yashan
   ```

## Disabling *yasom* Automatic Leader Election

To disable the *yasom* automatic leader election feature, log in to the primary *yasom* device and execute the following command:

```shell
$ yasboot process yasom election off -c yashandb

Connecting to master yasom...
Disabling election on all yasom...

Election disable result:
  host0005 (192.168.1.5:2775)
  host0006 (192.168.1.6:1675)
  host0007 (192.168.1.7:1675)

Election disabled successfully on all yasom
```