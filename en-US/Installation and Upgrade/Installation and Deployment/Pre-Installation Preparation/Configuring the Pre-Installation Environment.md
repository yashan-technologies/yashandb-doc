## Open Ports

Running YashanDB requires the use of a series of ports (the functions and related information of these ports are listed in the [Port List](../../../Reference Manual/Port List)). YashanDB has established a set of port allocation rules and provides default port values that need to be specified during the installation process. Users may modify the port numbers according to their own network planning, but should adhere to the port allocation rules to avoid port conflicts.

The port allocation rules and default values are as follows:

- The database listening port is specified by the `beging-port`/start port parameter in the installation steps, with a default value of 1688.

- The database listening port will serve as the initial value for calculating other internal communication ports.

    - In Standalone Deployment, if the SQL mode is specified as [mysql mode](../../../Product Overview/Compatibility/Compatibility with MySQL), a set of listening ports for mysql protocol usage should also be planned, with a default value of 1690 and each standby database defaulting to +3, and so on.

    - In ISC Distributed Cluster Deployment, port numbers are generated in the order of MN, CN, DN, while internal communication ports are generated in the order of inter-group communication and intra-group communication. When deploying multiple DN groups, each DN group defaults to +3, and so on.

    - In YAC Deployment, the VIP listening port is the same as the database listening (LISTEN_ADDR) port, and no additional port numbers need to be allocated.

    |Deployment Type |Database Listening |Internal Communication |yasom |yasagent |
    | -------- | ----------------------------- | ----- |--|--|
    | Standalone Deployment      | yashan mode: 1688 <br/> mysql mode: 1688 and 1690, where 1690 is the default listening port for mysql protocol | replication link (required for primary/standby deployment): initial value +1, defaulting to 1689 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |
    | YAC Deployment             | 1688                                   | * Communication between database instances within the same cluster: initial value +1, defaulting to 1689<br/>* Communication between YCS instances: initial value +100, defaulting to 1788<br/>* replication link (required for primary/standby deployment): initial value +2, defaulting to 1690 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |
    | ISC Distributed Cluster Deployment     | MN: initial value -10, defaulting to 1678<br/>CN: 1688<br/>DN: initial value +10, defaulting to 1698 | Corresponding listening ports incremented by +1, generated in the order of inter-group communication and intra-group communication<br/><br/>* Inter-group communication: MN is 1679, CN is 1689, DN is 1699<br/>* Intra-group communication: MN is 1680, CN is 1690, DN is 1700 | initial value -13, defaulting to 1675 | initial value -12, defaulting to 1676 |

- If a server requires visual deployment web services, port 9001 should also be used.

After completing the port planning, the corresponding ports need to be opened on **all servers**.

### Method 1: Disable Firewall

Execute the following command on all servers to disable the firewall:

```shell
## Disable firewall
# systemctl stop firewalld 
## Disable auto-start
# systemctl disable firewalld
```

### Method 2: Add to Whitelist

If the firewall cannot be disabled, ports must be added to the whitelist as follows:

1. Check the open ports of the firewall:

    ```shell
    # firewall-cmd --zone=public --list-ports
    ```

2. Add the port to the firewall:

    The following example demonstrates how to add port 1688 to the firewall; the method is the same for other ports.

    ```shell
    ## Add (--permanent means it will take effect permanently; without this parameter, it will be invalid after a restart)
    # firewall-cmd --zone=public --add-port=1688/tcp --permanent
    ## Reload
    # firewall-cmd --reload
    ## Check
    # firewall-cmd --zone=public --query-port=1688/tcp
    ```

    > **Note**:
    >
    > Opening ports by adding to the whitelist may still result in deployment failure due to communication issues during the installation process. In such cases, contact the company's network administrator to clarify the reasons and re-open the ports before proceeding with installation.

3. To remove a port that has been added to the whitelist, use the following command:

    ```shell
    # firewall-cmd --zone=public --remove-port=1688/tcp --permanent
    ```

## Enable SSH Service

Check whether the SSH service on all servers is active:

```shell
# systemctl status sshd.service
```

If the output does not contain `active (running)`, use the following command to enable the SSH service:

```shell
# systemctl start sshd.service
```

> **Note**: 
>
> This installation manual assumes the default SSH port number is 22. If the actual planning is for a different port number, manual specification will be required during the installation process.

## Clean Environment (YAC Deployment)

If deploying a YashanDB YAC, shared memory needs to be cleared on all servers.

```shell
# ipcrm -a
```
