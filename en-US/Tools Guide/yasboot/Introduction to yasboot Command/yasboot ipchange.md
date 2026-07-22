When the server IP address in the database cluster managed by *yasboot* changes, users can invoke the commands `ipchange yasom` and `ipchange yasagent` to update the IP configuration on the yasom side, ensuring that the server can still be managed by *yasboot*. Additionally, use `ipchange host` to update the IP configuration on the YashanDB side, allowing the cluster to operate normally under the new IP address.

The order of executing the IP change is yasom > yasagent > host. If there is no need to update the IP of a particular tool/server, it can be skipped, and the update can proceed to the next tool/server, but the order must not be mixed.

This command is applicable only to Standalone Deployment.

## ipchange yasom

This command is used to change the IP of the main yasom and restart yasom. After restarting, yasom will listen on the new IP address and the original port.

|Option |Meaning |
| ----------------   | -----------------------------  |
| *-t, --toml*        | Path to the server configuration file `hosttoml` (required)  |
| *-n, --new-ip*      | The new IP of the server where the main yasom is located (required)  |
| *-h,--help*          | View help information for the current command  |

***Example***: 

```shell
$ yasboot ipchange yasom -t hosts.toml -n 192.168.1.2

stop yasom successfully
start yasom successfully
restart yasom successfully
yasom change ip success
```

## ipchange yasagent

This command is used to change the IP of yasagent and restart yasagent. After restarting, yasagent will listen on the new IP address and the original port.

This command can only change the IP of yasagent on one server at a time. To update the IP of yasagent on multiple servers, this command must be executed for each server where the IP needs to be changed.

|Option |Meaning |
| ----------------   | -----------------------------  |
| *-t, --toml*        | Path to the server configuration file `hosttoml` (required)  |
| *-n, --new-ip*      | The new IP of the server on which yasagent is located (required)  |
| *--host-id*         | The ID of the server where yasagent is located, for example: host0001, can be obtained from the hosts.toml file (required)  |
| *-h,--help*          | View help information for the current command  |

***Example***: 

```shell
$ yasboot ipchange yasagent -t hosts.toml -n 192.168.1.2 --host-id host0001

stop yasagent successfully
start yasagent successfully
restart yasagent successfully
yasagent change ip success
```

## ipchange host

Prerequisites for using this command to change the server IP:

- If changing the IP of yasom, the corresponding operation must have been completed.
- If changing the IP of yasagent, the IP of each yasagent to be changed must have been updated.

This command is used to change the IP of the database instance, and using this command will automatically shut down the database instance.

|Option |Meaning |
| ----------------   | -----------------------------  |
| *-t, --toml*        | Path to the server configuration file `hosttoml` (required)  |
| *-l, --listen-ip*   | The new IP for the database listening address, either this or --listen-cidr must be specified  |
| *-r, --replica_ip*  | The new IP for the primary-standby replication link address, either this or --replica-cidr must be specified  |
| *-lc, --listen-cidr*| The new CIDR for the database listening address, multiple can be specified, e.g., 192.168.1.2/24,0.0.0.0/0; either this or --listen-ip must be specified |
| *-rc, --replica-cidr*| The new CIDR for the primary-standby replication link address, multiple can be specified, e.g., 192.168.1.2/24,0.0.0.0/0; either this or --replica-ip must be specified |
| *--host-id*         | The server ID, can be obtained from the hosts.toml file, for example: host0001 |
| *-s, --start*       | Automatically restart the database after the IP change                                |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-h,--help*          | View help information for the current command  |

When neither `--listen-ip` nor `--listen-cidr` is specified, yasom will check if the current database listening address IP is available. If it is available, no modification will be made. If it is not available, it will be changed to the IP of yasagent. The same applies to `--replica-ip` and `--replica-cidr`.

***Example***: 

1. Changing IP.

   Scenario 1: Use a new IP to change the database instance IP on a server (e.g., host0001).
    ```shell
    $ yasboot ipchange host -t hosts.toml -l 192.168.1.2 -r 192.168.1.2 --host-id host0001

    host host0001 change ip success
    ```

   Scenario 2: Use a new CIDR to change the database instance IP on a server (e.g., host0001).
    ```shell
    $ yasboot ipchange host -t hosts.toml -lc 192.168.1.1/24 -rc 192.168.1.1/24 --host host0001

    new replication ip is 192.168.1.2
    new listen ip is 192.168.1.2
    host0001 change ip success
    ```

   Scenario 3: Use the IP of yasagent to change all database instances IP at once.
    ```shell
    $ yasboot ipchange host -t hosts.toml

    host0001 change ip success
    ```

   Scenario 4: Use a new CIDR to change all database instances IP at once.
    ```shell
    $ yasboot ipchange host -t hosts.toml -lc 192.168.1.1/24,192.168.2.1/24 -lr 192.168.1.1/24,192.168.2.1/24

    host0001 change ip success
    ```

2. Restarting the database instance after the IP change.

   - Method 1: When changing the last server IP, use the `-s` option to start the database after the change is completed.
    ```shell
    $ yasboot ipchange host -t hosts.toml -l 192.168.1.2 -r 192.168.1.2 --host-id host0001 -s

    host host0001 change ip success
    start cluster now
    +-----------------------------------------------------------------------------------------------------------+
    | type | uuid             | name              | hostid | index    | status  | return_code | progress | cost |
    +-----------------------------------------------------------------------------------------------------------+
    | task | 01dd119a4034dd02 | StartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 1    |
    +------+------------------+-------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```

   - Method 2: After all changes are completed, use the [cluster start](yasboot cluster) command to manually start the database instance.
    ```shell
    $ yasboot cluster start -c yashandb

    +-----------------------------------------------------------------------------------------------------------+
    | type | uuid             | name              | hostid | index    | status  | return_code | progress | cost |
    +-----------------------------------------------------------------------------------------------------------+
    | task | 01dd119a4034ru01 | StartYasdbCluster | -      | yashandb | SUCCESS | 0           | 100      | 1    |
    +------+------------------+-------------------+--------+----------+---------+-------------+----------+------+
    task completed, status: SUCCESS
    ```
