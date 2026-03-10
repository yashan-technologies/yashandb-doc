The YashanDB installed via [command line](../YashanDB Installation via CLI/00YashanDB Installation via CLI) does not default to configuring the daemon process to start automatically at boot. After the server restarts for various reasons, the installation user must log into the database installation server and manually execute the following commands to start the database:

::: tabs

== Standalone Deployment, ISC Distributed Cluster Deployment 

```shell
# Manually start the yasom and yasagent processes
$ yasboot process yasom start -c yashandb -t /home/yashan/install/hosts.toml
$ yasboot process yasagent start -c yashandb -t /home/yashan/install/hosts.toml

# The database can only be started after the yasom and yasagent processes are started
$ yasboot cluster start -c yashandb

# Start the daemon process as needed
$ yasboot monit start --cluster yashandb
```

== YAC Deployment / Distributed Cluster Deployment

```shell
# If VIP or I/O Fencing based on SCSI persistent reservations is needed, start the *ycsrootagent* process (must specify the path under $YASCS_HOME)
$ echo $YASCS_HOME
/data/yashan/yasdb_data/ycs/ce-1-1

$ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1 &

# Manually start the yasom and yasagent processes
$ yasboot process yasom start -c yashandb -t /home/yashan/install/hosts.toml
$ yasboot process yasagent start -c yashandb -t /home/yashan/install/hosts.toml

# The database can only be started after the yasom and yasagent processes are started
$ yasboot cluster start -c yashandb
# If NVMe information loss in Distributed Cluster causing startup failure, you can execute the following command to restore configuration, then restart the database
$ yasboot nvme rebuild -c yashandb --cn --dn

# Start the daemon process as needed
$ yasboot monit start --cluster yashandb
```

:::

To simplify operations and maintenance, it is recommended to configure the daemon process to start automatically at boot on each server. After the daemon process starts, it will launch other processes of YashanDB, thereby indirectly achieving the automatic startup of the database and related services:

::: tabs

== Standalone Deployment, ISC Distributed Cluster Deployment 

|Process |Process Description |
|----------|--------------------------------------|
| monit process | Used to monitor database-related processes, including yasdb, yasom, and yasagent processes. |

== YAC Deployment / Distributed Cluster Deployment

|Process |Applicable Scenarios |Process Description |
|----------|----------|--------------------------------------|
| monit process | General | Used to monitor database-related processes, including ycsm, yasom, and yasagent processes. |
| ycsrootagent process | [SCAN](../../../共享集群/集群服务管理/SCAN管理) enabled, [VIP](../../../共享集群/集群服务管理/VIP管理) enabled or [I/O Fencing functionality based on SCSI persistent reservations](../../../共享集群/集群服务管理/IO Fencing/基于SCSI持久预留的IO Fencing) | Provides corresponding services for I/O Fencing functionality based on SCSI persistent reservations. <br/> This process cannot be managed by YAC node start/stop commands and cannot be monitored by the yascsm process. During installation, *yasboot* will automatically attempt to start *ycsrootagent* with sudo; if this process encounters exceptions or if the server restarts, it needs to be manually started with sudo (requires root privilege). |

:::

## Enable Auto-Start

>**Note**:
>
> This chapter uses configuring systemd service auto-start at boot via systemctl as an example. Users may configure it as needed according to their actual usage habits. **Do not use multiple methods to configure auto-start at the same time**, as this may start multiple similar processes and cause exceptions.

### Step 1: Create Boot Auto-Start Service Script



1. Create and edit the service startup script.

    The content of the script is for example only, and the path, variable values, and other information must be modified according to the actual situation.

    ```shell
    $ sudo vi /usr/local/bin/yashan_monit.sh
    ```


    ::: tabs

== Standalone Deployment, ISC Distributed Cluster Deployment Script Example



```bash
#!/bin/bash
MONIT_AUTOSTART="true"                  # Whether to monitor the monit process, true means yes, other values mean no
YASDB_USER=yashan                               # YashanDB installation user
YASDB_HOME=/data/yashan/.yasboot/{cluster_name}_yasdb_home     # YashanDB HOME directory
INTERVAL=3                                # Monitoring interval, in seconds, periodically check the corresponding process by name; if abnormal, restart

# Load YashanDB environment variables
env LD_LIBRARY_PATH=$YASDB_HOME/lib

while true; do    
    if "$MONIT_AUTOSTART" = "true"; then
        if ! pgrep -a monit | grep "$YASDB_HOME" > /dev/null; then
            echo "$(date) monit abnormal, try restart..." 
            su - $YASDB_USER -c "source ~/.bashrc && $YASDB_HOME/om/bin/monit -c $YASDB_HOME/om/monit/monitrc" &
        fi
    fi
    sleep "$INTERVAL"
done
```


	== YAC Deployment / Distributed Cluster Deployment Script Example

```bash
#!/bin/bash
MONIT_AUTOSTART="true"                  # Whether to monitor the monit process, true means yes, other values mean no
YCSROOTAGENT_AUTOSTART="true"           # Whether to monitor the *ycsrootagent* process, true means yes, other values mean no
YASDB_USER=yashan                               # YashanDB installation user
YASDB_HOME=/data/yashan/.yasboot/{cluster_name}_yasdb_home     # YashanDB HOME directory
YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1   # Directory corresponding to the ycsrootagent process
INTERVAL=3                                # Monitoring interval, in seconds, periodically check the corresponding process by name; if abnormal, restart

while true; do    
    if "$MONIT_AUTOSTART" = "true"; then
        if ! pgrep -a monit | grep "$YASDB_HOME" > /dev/null; then
            echo "$(date) monit abnormal, try restart..." 
            su - $YASDB_USER -c "source ~/.bashrc && $YASDB_HOME/om/bin/monit -c $YASDB_HOME/om/monit/monitrc" &
        fi
    fi
    if "$YCSROOTAGENT_AUTOSTART" = "true"; then
        if ! pgrep -a ycsrootagent | grep "$YASCS_HOME" > /dev/null; then
            echo "$(date) ycsrootagent abnormal, try restart..."
            env LD_LIBRARY_PATH=$YASDB_HOME/lib $YASDB_HOME/bin/ycsrootagent start -H $YASCS_HOME &
        fi
    fi
    sleep "$INTERVAL"
done
```

    :::



2. Grant the script executable privilege.

    ```shell
    $ sudo chmod +x /usr/local/bin/yashan_monit.sh
    ```



### Step 2: Create Service Using systemd

Create and edit the service unit configuration file.



```shell
$ sudo vi /etc/systemd/system/yashan_monit.service

[Unit]
Description=Yashan Monitor
After=network.target

[Service]
ExecStart=/usr/local/bin/yashan_monit.sh
Type=simple
Restart=always
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```


<span id="enable" name="enable" class="yaslink"></span>

### Step 3: Enable the Service



1. Activate and start the service.

    ```shell
    $ sudo systemctl daemon-reload

    $ sudo systemctl enable yashan_monit
    Created symlink from /etc/systemd/system/multi-user.target.wants/yashan_monit.service to /etc/systemd/system/yashan_monit.service.

    $ sudo systemctl start yashan_monit
    ```





2. Check the service status.

    ```shell
    $ systemctl status yashan_monit
    yashan_monit.service - Yashan Monitor
    Loaded: loaded (/etc/systemd/system/yashan_monit.service; enabled; vendor preset: disabled)
    Active: active (running) since Thu 2025-06-12 10:41:01 CST; 4s ago
    Main PID: 3580 (yashan_monit.sh)
    CGroup: /system.slice/yashan_monit.service
           ├─3580 /bin/bash /usr/local/bin/yashan_monit.sh
           └─3589 sleep 3
    ```


<span id="disable" name="disable" class="yaslink"></span>

## Disable Auto-Start

If the auto-start at boot is no longer needed, execute the following commands to disable the service.



```shell
$ sudo systemctl stop yashan_monit

$ sudo systemctl disable yashan_monit
Removed symlink /etc/systemd/system/multi-user.target.wants/yashan_monit.service.

$ systemctl status yashan_monit
● yashan_monit.service - Yashan Monitor
   Loaded: loaded (/etc/systemd/system/yashan_monit.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
```

