yasom is an independent process that supports primary/standby (primary/secondary). There can be only one primary yasom process globally, with N standby yasom processes (N ≥ 0, default is 0).

In the same database environment, at most one yasom process can run on each server.

## Manually Add yasom

### Add Standby yasom

Currently, it is not possible to directly deploy a standby yasom. A standby yasom can only be added on the current server using the [recover](../Introduction to yasboot Command/yasboot process) command, provided that there are no existing yasom processes on the current server.

1. Log in to the database server (any one) as the installation user.

2. Check yasom process information.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary | local_yasom_addr  | role    | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675  | []        | 192.168.1.2:1675 | primary | 2          | 39      | on          |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    | host0002 | -     | 192.168.1.3  | 192.168.1.2:1675 | []        | -                 | -       | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+-----------+-------------------+---------+------------+---------+-------------+
    ```
    
    Based on the information displayed, there is no yasom process on host0002, allowing for the addition of a standby yasom. If each server has an existing yasom process, subsequent operations cannot be performed.

3. Log in to host0002 as the installation user and execute the following command to add a standby yasom on the host0002 server.

    ```shell
    # 192.168.1.3 is the IP address of host0002, and 1675 is the planned yasom port, both should be replaced with actual values
    $ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675
    # Enter yes when prompted
    Are you sure you want to use this backup data to recover secondary yasom? [yes/no]: yes
    recover secondary yasom success
    ```

4. Check if the yasom process was created successfully.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary           | local_yasom_addr  | role      | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2 :1675 | [192.168.1.3 :1675] | 192.168.1.2 :1675 | primary   | 2          | 39      | on          |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+
    | host0002 | 31246 | 192.168.1.3  | 192.168.1.2 :1675 | [192.168.1.3 :1675] | 192.168.1.3 :1675 | secondary | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+
    ```

### Add Primary yasom (Repair yasom without a primary)

The operation to add a primary yasom is used only for manually repairing the issue of yasom without a primary.

>**Caution**:
>
> If there is currently a primary yasom in the environment, there is no need, and it is not possible, to add a primary yasom.

#### Method 1: Promote Existing Standby yasom to Primary

1. Log in to the database server where the target standby yasom is located as the installation user.

2. Execute the following command to promote the standby yasom to primary.

    ```shell
    $ yasboot process yasom recover -c yashandb --role primary
    ```

#### Method 2: Directly Add New Primary yasom

1. Log in to a database server where yasom does not exist as the installation user.

2. Execute the following command to add a primary yasom on the current server.

    ```shell
    # 192.168.1.3 is the IP address of host0002, and 1675 is the planned yasom port, both should be replaced with actual values
    $ yasboot process yasom recover -c yashandb -l 192.168.1.3:1675  --role primary
    # Enter yes when prompted
    Are you sure you want to use this backup data to recover primary yasom? [yes/no]: yes
    recover primary yasom success
    ```

<span id="demote" name="demote" class="yaslink"></span>

## Demote Primary yasom to Standby (Repair Multiple Primary yasom Issue)

The demotion operation is mainly used to manually repair the issue of multiple primary yasom processes. It is recommended to use [yasom auto-repair](Configuring Auto-Healing Function for yasom) to reduce manual maintenance costs. If the auto-repair does not resolve the issue, it is necessary to manually demote the extra primary yasom or [clean up](#clean).

>**Caution**:
>
> If there is only one primary yasom in the environment, there is no need, and it is not possible, to perform demotion.

1. Log in to any database server as the installation user.

2. Check yasom process information.

    ```shell
    yasboot process yasom status -c yashandb
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary           | local_yasom_addr  | role      | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675 | [] | 192.168.1.2:1675 | primary | 2          | 39      | off         |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+
    | host0002 | 31246 | 192.168.1.3  | 192.168.1.3:1675 | [] | 192.168.1.3:1675 | primary | 2          | 39      | -           |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+
    ```

3. Log in to the server where the yasom that needs to be demoted is located, and execute the following command to demote it:

    ```shell
    $  yasboot process yasom demote -c yashandb
    ```

<span id="clean" name="clean" class="yaslink"></span>

## Clean Up Extra yasom

> **Caution**: 
>
> If there is only one primary yasom in the environment, it is not possible to perform the clean-up operation.

1. Log in to the database server where the target standby yasom is located as the installation user.

2. Execute the following command to clean up the yasom process.

    ```shell
    $ yasboot process yasom clean -c yashandb
    all yasom status is as follows:
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | hostid   | pid   | ipaddr       | primary           | secondary           | local_yasom_addr  | role      | backup_num | max_seq | auto_repair |
    +------------------------------------------------------------------------------------------------------------------------------------------------+
    | host0001 | 15080 | 192.168.1.2  | 192.168.1.2:1675 | [192.168.1.3:1675]  | 192.168.1.2:1675  | primary   | 9          | 50      | on          |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+
    | host0002 | 4343  | 192.168.1.3  | 192.168.1.2:1675 | [192.168.1.3:1675]  | 192.168.1.3:1675  | secondary | 9          | 50      | -           |
    +----------+-------+--------------+-------------------+---------------------+-------------------+-----------+------------+---------+-------------+

    yasom 192.168.1.2:1675 can see information is as follows:
    +------------------------------------------------------------------------------------------+
    | hostid   | hostname | ipaddr       | node_type | nodeid | data_path                      |
    +------------------------------------------------------------------------------------------+
    | host0001 | host0001 | 192.168.1.2  | db        | 1-1:1  | /data/yashan/yasdb_data/db-1-1 |
    +----------+----------+--------------+-----------+--------+--------------------------------+
    | host0002 | host0002 | 192.168.1.3  | db        | 1-2:2  | /data/yashan/yasdb_data/db-1-2 |
    +----------+----------+--------------+-----------+--------+--------------------------------+
    incr seq: 50, update at: 2025-07-11 16:44:13

    you are preparing to clean secondary yasom: 192.168.1.3:1675
    after clean, new primary yasom: 192.168.1.2:1675
    after clean, new secondary yasom: []
    # Enter yes when prompted
    Are you sure you want to clean this yasom? [yes/no]: yes
    clean yasom success
    ```
