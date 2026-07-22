## Enable yasom Self-Healing

Enabling yasom self-healing functionality allows for automatic repair in the event of a yasom multi-master exception, keeping one primary yasom and automatically demoting the others to standby. The demoted yasom will back up its metadata to $YASDB_HOME/om/{cluster_name}/data/repair/.

If the database cluster information is not as expected after self-healing, you can use the yasboot cluster load command to reload the information.

### Prerequisites

- Self-healing and yasom arbitration (yasboot election) are mutually exclusive. If yasom arbitration is already enabled ([*yasom* Election](../../../High Availability/Configuring Leader Election/Configuring yasom Election)), self-healing cannot be used.

- The [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) must be enabled (it is enabled by default when following the standard installation steps) for the self-healing functionality to work properly.

### Steps

1. Log in to the database server as the installation user.

2. Check the yasom process information.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary | local_yasom_addr  | role    | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675  | []        | 192.168.1.2:1675 | primary | 2          | 39      | off         |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    | host0002 | -     | 192.168.1.3  | 192.168.1.2:1675  | []        | -                 | -       | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    ```

3. On any server where a yasom process is running, execute the following command to enable self-healing functionality.

    ```shell
    yasboot process yasom repair on -c yashandb
    enable repair success
    ```

4. Check if the configuration has taken effect.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary | local_yasom_addr  | role    | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675  | []        | 192.168.1.2:1675 | primary | 2          | 39      | on          |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    | host0002 | -     | 192.168.1.3  | 192.168.1.2:1675  | []        | -                 | -       | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    ```

    If auto_repair = on, it indicates successful enabling, and only the primary yasom will display this status.

## Disable yasom Self-Healing

1. Log in to the database server as the installation user.

2. Check the yasom process information.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary | local_yasom_addr  | role    | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675  | []        | 192.168.1.2:1675 | primary | 2          | 39      | on          |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    | host0002 | -     | 192.168.1.3  | 192.168.1.2:1675  | []        | -                 | -       | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    ```

3. On any server where a yasom process is running, execute the following command to disable self-healing functionality.

    ```shell
    yasboot process yasom repair off -c yashandb
    disable repair success
    ```
