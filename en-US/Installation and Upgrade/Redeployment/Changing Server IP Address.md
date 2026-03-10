Online redeployment is a planned activity that should be designed and executed along with its dependent pre-operations (such as server IP changes). During this period, the database service should not be stopped, but no business operations should take place.

YashanDB provides the *yasboot* tool and a series of command options to quickly call for online redeployment operations on the database when necessary. This operation is only applicable to Standalone Deployment.

This article mainly describes how administrators can perform database-level IP changes using the [yasboot ipchange command](../../Tools Guide/yasboot/Introduction to yasboot Command/yasboot ipchange) when one or more server IPs in a Standalone Deployment database cluster change, ultimately restoring the database to availability.

The following operations will be described using the example of two server IP changes in a one primary two backup environment:

|Server Name |Old IP |Role |New IP |
| -------- | --------------- | ---- | --------------- |
| Server 1    | **192.168.1.2** | Primary | **192.168.1.5** |
| Server 2    | **192.168.1.3** | Backup | **192.168.1.6** |
| Server 3    | 192.168.1.4     | Backup | 192.168.1.4   |

The order of executing IP changes is yasom > yasagent > host. If the IP of a certain tool/server does not need to be updated, it can be skipped and the next tool/server's IP can be updated, but the order cannot be rearranged.

## Prerequisites

Before executing the following operations, ensure that the environment for the new IP meets all deployment requirements of YashanDB. For details, please refer to [Pre-Installation Preparation](../Installation and Deployment/Pre-Installation Preparation/00Pre-Installation Preparation).

## Step 1: Enter the YashanDB Product Installation Directory

The online redeployment needs to be performed in the installation directory where YashanDB was installed. Log in to the server as the yashan user and enter the install directory:

```shell
$ cd /home/yashan/install
```

## Step 2: Change yasom IP

1. View the server information of the main yasom process.

   The relevant information of the server where the main yasom process resides can be obtained by checking the following content in the hosts.toml file in the installation directory:

    ```toml
    [om]
      hostid = "host0001"
      [om.config]
        LISTEN_ADDR = "192.168.1.2:1675"
    ```

   According to the above information, yasom is deployed on the server with hostid host0001, and the IP is 192.168.1.2, which needs to be changed.

2. Change the main yasom process IP.

    ```shell
    $ cd /home/yashan/install
    $ yasboot ipchange yasom -t hosts.toml -n 192.168.1.5
    ```

   After the change is completed, yasom can provide services normally.

## Step 3: Change yasagent IP

1. View the server information of the yasagent service.

   The relevant information of the server where the yasagent service resides can be obtained by checking the following content in the hosts.toml file in the installation directory:

    ```toml
    [[host]]
      hostid = "host0001"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.2:1676"

    [[host]]
      hostid = "host0002"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.3:1676"

    [[host]]
      hostid = "host0003"
    [host.yasagent]
        [host.yasagent.config]
          LISTEN_ADDR = "192.168.1.4:1676"

    ```

   According to the above information, yasagent on servers with hostid host0001 and host0002 used the old IPs (192.168.1.2 and 192.168.1.3) and need to be changed. The yasagent on the server with hostid host0003 does not need to change the IP and can provide services normally.

2. Execute the following commands to change the IP of yasagent on host0001 and host0002 in sequence.

    ```shell
   # Change yasagent IP on host0001 server
    $ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.5 --host-id host0001

   # Change yasagent IP on host0002 server
    $ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.6 --host-id host0002
    ```

   After the change is completed, all yasagent services can provide services normally.

## Step 4: Change yasdb IP

Changing the yasdb IP includes changing the database listening address and primary-standby replication link address of the yasdb process. The server information where the yasdb process resides is the same as that of the yasagent service.

> **Note**:
>
> When changing the yasdb IP, all yasdb processes will be stopped, and the database service will be shut down.

```shell
# Change yasdb IP on host0001 server
$ yasboot ipchange host -t hosts.toml -l 192.168.1.5 -r 192.168.1.5 --host-id host0001

# Change yasdb IP on host0002 server
$ yasboot ipchange host -t hosts.toml -l 192.168.1.6 -r 192.168.1.6 --host-id host0002
```

## Step 5: Start the Database

Execute the following command to start the database service:

```shell
$ yasboot cluster start -c yashandb
```

## Step 6: Check the IP Change Result

Execute the following command to query information about the database cluster and verify whether the IP has been changed:

```shell
$ yasboot cluster status -c yashandb -d
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| hostid   | node_type | nodeid | pid    | instance_status | database_status | database_role | listen_address   | data_path                       |
+-------------------------------------------------------------------------------------------------------------------------------------------------+
| host0001 | db        | 1-1:1  | 114864 | open            | normal          | standby       | 192.168.1.5:1688 | /data/yashan/yasdb_data/db-1-1  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
| host0002 | db        | 1-2:2  | 114862 | open            | normal          | standby       | 192.168.1.6:1688 | /data/yashan/yasdb_data/db-1-2  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
| host0003 | db        | 1-3:3  | 6921   | open            | normal          | primary       | 192.168.1.4:1688 | /data/yashan/yasdb_data/db-1-3  |
+----------+-----------+--------+--------+-----------------+-----------------+---------------+------------------+---------------------------------+
```
