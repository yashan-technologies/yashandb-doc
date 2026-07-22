YashanDB high availability architecture provides three protection modes: maximize performance (MAXIMIZE PERFORMANCE), maximize availability (MAXIMIZE AVAILABILITY), and maximize protection (MAXIMIZE PROTECTION).

## Initial Protection Mode

### Standalone Deployment

During installation and deployment, *yasboot* automatically adjusts the protection mode based on the number of primary and standby nodes:

- Single instance: *yasboot* does not adjust the protection mode; the default is maximize performance.

- One primary/one standby: *yasboot* adjusts the protection mode to maximize availability.

- One primary/multi-standby (excluding cascade standby): *yasboot* adjusts the protection mode to maximize protection and enables [leader election](Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

### YAC Deployment

During installation and deployment, *yasboot* does not automatically adjust the protection mode; the default is maximize performance.

### ISC Distributed Cluster Deployment

During installation and deployment, *yasboot* automatically adjusts the protection mode based on the number of nodes in the MN group and DN group:

- One node: *yasboot* does not adjust the protection mode; the default is maximize performance.

- Two nodes: *yasboot* adjusts the protection mode to maximize availability.

- Three or more nodes: *yasboot* adjusts the protection mode to maximize protection and enables [leader election](Configuring Leader Election/Configuring Leader Election for One Primary and Multi-Standby).

## View Current Protection Mode

You can obtain the current protection mode information by querying the [GV$DATABASE](../Reference Manual/System Views/Dynamic Performance Views/GV$DATABASE)/[V$DATABASE](../Reference Manual/System Views/Dynamic Performance Views/V$DATABASE) view:

- The PROTECTION_MODE field indicates the protection mode set on the current node.

    - MAXIMUM PERFORMANCE: maximize performance

    - MAXIMUM AVAILABILITY: maximize availability

    - MAXIMUM PROTECTION: maximize protection

- The PROTECTION_LEVEL field indicates the effective protection mode and level (the synchronization state of redo logs).

    * UNPROTECTED: the database is not yet opened to the OPEN phase.

    * MAXIMUM PERFORMANCE: maximize performance mode is active.

    * MAXIMUM AVAILABILITY: maximize availability mode is active, and redo has been synchronized to the corresponding standby database.

    * RESYNCHRONIZATION: maximize availability mode is active, but redo has not yet been synchronized to the corresponding standby database.

    * MAXIMUM PROTECTION: maximize protection mode is active, and redo synchronization is normal.

    * SYNCHRONIZATION BLOCKING: maximize protection mode is active, but redo synchronization is abnormal, causing transaction commits to block.

```sql
-- Query the protection mode and level of the current node
SELECT PROTECTION_MODE,PROTECTION_LEVEL FROM V$DATABASE;

PROTECTION_MODE                                                  PROTECTION_LEVEL                                       
---------------------------------------------------------------- ----------------------------------------------------------------
MAXIMUM AVAILABILITY                                             MAXIMUM AVAILABILITY
```

## Switch Protection Mode

Execute the [ALTER DATABASE SET STANDBY DATABASE TO](../Development Guide/SQL Reference Manual/SQL Statements/ALTER DATABASE.md#set_standby_clause) statement to switch the database protection mode as needed.

In high availability deployment, the protection mode of the primary node is synchronized to the standby node through a heartbeat.

### Prerequisites

- Database operational status requirements:

    - In Standalone Deployment and ISC Distributed Cluster Deployment, operations can be performed in OPEN or MOUNT phase.

    - In YAC Deployment, to set to maximize protection mode, all surviving instances must be in OPEN phase. For other modes, operations can be performed in either OPEN or MOUNT phase.

- In maximize availability or maximize protection modes, primary database transaction commits need to wait for redo synchronization to the respective standby database (MAJORITY or [custom synchronous standby](Defining Synchronous Standbys)). Therefore, when switching protection modes in the OPEN phase of the database, it must be ensured that primary database redo has been synchronized to the corresponding standby database; otherwise, the switch will fail. It is recommended to first switch to maximize availability to transition and wait for redo synchronization to complete (i.e., when the PROTECTION_LEVEL of V$DATABASE changes to MAXIMUM AVAILABILITY) before switching to the final target protection mode.

- In ISC Distributed Cluster Deployment, switching protection mode is not recommended; if needed, it can only be operated via direct connection to the MN or DN nodes. The MN group is fixed to use maximize protection mode (to maximize data loss prevention) and should not be configured by the user.

### Operation Steps

1. Connect and log in to the database as a DBA user.
    ```shell
    $ yasql sales/********@192.168.1.2:1688
    YashanDB SQL Enterprise Edition Release {version_number} x86_64

    Connected to:
    YashanDB Server Enterprise Edition Release {version_number} x86_64 - Linux

    SQL> 
    ```


2. Check the current protection mode and level:

    ```sql
    -- Query the protection mode and level of the current node
    SELECT PROTECTION_MODE,PROTECTION_LEVEL FROM V$DATABASE;

    PROTECTION_MODE                                                  PROTECTION_LEVEL                                       
    ---------------------------------------------------------------- ----------------------------------------------------------------
    MAXIMUM AVAILABILITY                                             MAXIMUM AVAILABILITY
    ```

    If this operation is executed during the OPEN phase of the database, confirm that PROTECTION_LEVEL = MAXIMUM AVAILABILITY before proceeding with subsequent operations.

3. Adjust the protection mode:

    ```sql
    ALTER DATABASE SET STANDBY DATABASE TO MAXIMIZE PERFORMANCE;
    ```

4. Confirm whether the protection mode switch has taken effect:

    ```sql
    SELECT PROTECTION_MODE,PROTECTION_LEVEL FROM V$DATABASE;
    PROTECTION_MODE                   PROTECTION_LEVEL                  
    --------------------------------- --------------------------------- 
    MAXIMUM PERFORMANCE               MAXIMUM PERFORMANCE              
    ```
