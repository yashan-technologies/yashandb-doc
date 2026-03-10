#### How to handle the prompt of host scan failure when generating the configuration file? 

The detailed error message may be `ip:192.168.1.2 scan failed, host scan failed: ssh: handshake failed: ssh: unable to authenticate, attempted methods [none password], no supported methods remain`.

1. Check if the SSH login username and password are entered correctly.

2. Refer to [Pre-Installation Preparation](Pre-Installation Preparation/Configuring the Pre-Installation Environment) for environment checks.

#### How to handle the prompt of host scan failure when accessing the user bastion host? 

The detailed error message may be `host scan failed: handshake failed: ssh: unable to authenticate, attempted methods [none password], no supported methods remain config check failed`.

1. Refer to [Pre-Installation Preparation](Pre-Installation Preparation/Configuring the Pre-Installation Environment) for environment checks.

2. Grant the installation user sudo privilege and configure SSH passwordless login.

3. If the issue persists, check the destination server's error log:

    ```shell
    cd /var/log
    tail -30 messages
    ```

    If the log shows `servername sshd[21921]: Authentication refused: bad ownership or modes for directory /home/username`, it may be a compatibility issue with the bastion host, and privileges need to be allocated in the bastion host resource management.

#### How to resolve the issue of deployment getting stuck for a long time? 

The time required for deployment is influenced by various factors such as server configuration and node scale.

If the wait time is long, you can use the following methods to understand the specific progress or status information:

- If you want to view the specific task progress during the wait, you can add the `-d,–child` parameter when executing the cluster deploy command.

- To view logs, you can check the yasagent.log file in the user-specified log-path/om/cluster-name.

#### How to view the cluster name, cluster information, and cluster status? 

You can check with `yasboot cluster status -c clustername -d`.

#### What are the differences and connections between `yasboot process monit` and `yasboot monit`? 

- yasboot process monit: Controls the monit process itself.

- yasboot monit: Manages the functionality of monit, such as the monit db process.

#### What are the differences between yasboot sql and *yasql*? In what scenarios are they used? 

Both have the same effect; yasboot sql will call *yasql* but is more convenient than *yasql*. For specifics, refer to [yasboot sql](../All Manuals/Tools Guide/yasboot/Introduction to yasboot Command/yasboot sql).

#### Does inconsistency in the sys password file of the primary Standby Database affect Primary/Standby synchronization? 

It does not affect synchronization, but it will cause the *yasboot* command to not work properly.

#### Will changing the database state to nomount using yasboot, and then altering the database to open affect the state recorded by *yasboot*? 

No.