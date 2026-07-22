YCS implements node-level and cluster-level parameter configuration through parameter files and the cluster registry, respectively.

This chapter will introduce the configuration parameters exposed by YCS and their adjustment methods. Please select the appropriate items and values based on the description to meet the operational requirements of YCS in your environment.

## Parameter Configuration and Management

### Managing Node-Level Parameters

The [ycsctl get](../../Tools Guide/ycsctl/User Guide for ycsctl/Node Management Commands.md) command can be used to view the configuration parameter values on the current node.

```shell
$ ycsctl get AUTO_START
```

The [ycsctl set](../../Tools Guide/ycsctl/User Guide for ycsctl/Node Management Commands.md) command can be used to modify the configuration parameter values on the current node (except for read-only parameters).

```shell
$ ycsctl set LOG_LEVEL DEBUG
```

### Managing Cluster-Level Parameters

Cluster-level parameters can only be modified offline. The specific operation process is as follows:

1. Stop the cluster.

2. Start the yasfs service.

    ```shell
    $ yasfs &
    ```

3. Execute the [ycsctl set_ycr](../../Tools Guide/ycsctl/User Guide for ycsctl/Cluster Configuration Commands) command to modify cluster configuration parameters.
   
    ```shell
    $ ycsctl set_ycr NETWORK_HB_TIMEOUT 50
    ```

4. Stop the yasfs service.

    ```shell
    $ yfscmd exec "shutdown abort"
    ```

5. Start the cluster.

## Parameter File

The name of the YCS parameter file is `yascs.ini`, which cannot be modified and must be located in the following directory structure on each server:

```text
# Please ensure that the YASCS_HOME directory has at least the following structure:
├── config
│   └── yascs.ini
├── instance
└── log
```

<span id="Node-Level" name="Node-Level"></span>

## Node-Level Parameters

### AUTO_START

*   Parameter Type: String

*   Default Value: ALWAYS  

*   Value Range/Format: ALWAYS, NEVER

*   Parameter Description: Indicates whether to start the database instance on this server when starting YCS. If not configured or set to ALWAYS, the database instance will start; if configured to NEVER, the database instance will not start.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### LOG_LEVEL

*   Parameter Type: String

*   Default Value: INFO

*   Value Range/Format: OFF, FATAL, ERROR, WARN, INFO, DEBUG, TRACE, ALL

*   Parameter Description: Specifies the level of the runtime log. OFF: disables the log; FATAL: log for fatal errors; ERROR: log for general errors; WARN: log for warning errors; INFO: normal operation log; DEBUG: debug log; TRACE: tracing log; ALL: enables all logs. The log levels increase in severity from left to right, with higher log levels including all lower log levels.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### LOG_NUMBER

*   Parameter Type: Numeric

*   Default Value: 10  

*   Value Range/Format: [2,10000]

*   Parameter Description: Specifies the number of runtime log files that can exist simultaneously.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### LOG_SIZE

*   Parameter Type: Numeric

*   Default Value: 20M

*   Value Range/Format: [1M,4G]

*   Parameter Description: Specifies the size of each runtime log file; exceeding this will create an archive and a new runtime log file.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RESTART_TIMES

*   Parameter Type: Numeric

*   Default Value: 3

*   Value Range/Format: [0,100]

*   Parameter Description: The number of times Monitor will retry to bring up the database instance when it unexpectedly drops.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RESTART_INTERVAL

*   Parameter Type: Numeric

*   Default Value: 30

*   Value Range/Format: [0,600]

*   Parameter Description: The wait time increment, in seconds, added after each retry to bring up the database instance. For example, after one restart, it will wait an increment before the next restart; after two restarts, it waits for two increments. As the number of restarts increases, the wait time grows in an arithmetic sequence.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### WAIT_STOP_FIN_TIME

*   Parameter Type: Numeric

*   Default Value: 90

*   Value Range/Format: [0,300]

*   Parameter Description: The time, in seconds, to wait after YCS executes the stop database script for it to fully stop. A value of 0 means to wait indefinitely until the normal stop concludes; other values mean that if the time elapses without stopping, a forced stop will occur.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="osw_auto_start" name="osw_auto_start"></span>

### OSW_AUTO_START

*   Parameter Type: String

*   Default Value: ON

*   Value Range/Format: [ON|OFF]

*   Parameter Description: Indicates whether to start OS Watcher when starting YCS; it will start by default.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="osw_interval" name="osw_interval"></span>

### OSW_INTERVAL

*   Parameter Type: Numeric

*   Default Value: [DISK_HB_KEEP_ALIVE](#disk_hb_keep_alive) / 3

*   Value Range/Format: [1,86400]

*   Parameter Description: The time interval, in seconds, between each information collection by OS Watcher. If this parameter is not configured, the actual time interval equals one-third (rounded down) of the disk heartbeat timeout DISK_HB_KEEP_ALIVE.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="osw_file_num" name="osw_file_num"></span>

### OSW_FILE_NUM

*   Parameter Type: Numeric

*   Default Value: 20

*   Value Range/Format: [2,10000]

*   Parameter Description: Specifies the number of data files of each type that OS Watcher can have simultaneously.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="osw_file_size" name="osw_file_size"></span>

### OSW_FILE_SIZE

*   Parameter Type: Numeric

*   Default Value: 20M

*   Value Range/Format: [1M,4G]

*   Parameter Description: Specifies the size of OS Watcher data files; exceeding this will create an archive and a new data file.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### YCR_FILE_NAME

*   Parameter Type: String

*   Default Value: +SYSTEM/ycr

*   Value Range/Format: This parameter must be equal to the filename specified when creating the ycr file.

*   Parameter Description: The root directory of YFS is `+`; the ycr file must be stored in the SYSTEM disk group.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### VOTING_FILE_NAME

*   Parameter Type: String

*   Default Value: +SYSTEM/voting

*   Value Range/Format: This parameter must be equal to the filename specified when creating the voting file.

*   Parameter Description: The root directory of YFS is `+`; the voting file must be stored in the SYSTEM disk group.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### _HOST_NAME

*   Parameter Type: String

*   Default Value: No default value

*   Value Range/Format: String length [4,64]

*   Parameter Description: A hidden parameter that may only be used in standalone multi-instance environments for ease of debugging by developers; **not recommended for use in user environments**.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### _MONITOR_SWITCH

*   Parameter Type: Numeric

*   Default Value: 0

*   Value Range/Format: [0,15]

*   Parameter Description: A hidden parameter used to disable certain monitoring switches for internal debugging; **not recommended for use in user environments**.

*   Immediate Effect of Changes: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### WORKER_LIMIT

*   Parameter Type: Numeric

*   Default Value: 0

*   Value Range/Format: [0, 18446744073709551615]

*   Parameter Description: The maximum number of SCAN VIP connection requests that a single node in the cluster can handle, 0 means no limit on the number of requests.

*   Immediate Effect of Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### _DB_START_PREFIX

*   Parameter Type: String

*   Default Value: Null

*   Value Range/Format: The string consists of 1-64 tokens separated by spaces, with a total length not exceeding 256 characters.

*   Parameter Description: A hidden parameter used to specify the command prefix when starting YCS to run the database, facilitating debugging for developers; **not recommended for use in user environments**.

*   Immediate Effect of Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="Cluster-Level" name="Cluster-Level"></span>

## Cluster-Level Parameters

<span id="network_hb_timeout" name="network_hb_timeout"></span>

### NETWORK_HB_TIMEOUT

*   Parameter Type: Numeric

*   Default Value: 30

*   Value Range/Format: [2,600]

*   Parameter Description: The network heartbeat timeout duration in seconds between YCS nodes and between YCS and the cluster database instances. Any failure to complete the network heartbeat exchange within this time will be regarded as an anomaly and handled as a fault. This parameter is uniform across the cluster and allows for offline modification.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

<span id="disk_hb_keep_alive" name="disk_hb_keep_alive"></span>

### DISK_HB_KEEP_ALIVE

*   Parameter Type: Numeric

*   Default Value: 60

*   Value Range/Format: [2,600]

*   Parameter Description: The disk heartbeat timeout duration, where the interval at which healthy YCS nodes update the disk heartbeat must not exceed this value, in seconds. Failure to update the disk heartbeat within this time will be regarded as an anomaly and processed as a fault. This parameter is uniform across the cluster and allows for offline modification.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

> **Note**: 
>
> - The default value for disk heartbeat timeout is a verified stable threshold. If you need to change it to a value lower than the default, stability testing for storage latency must be conducted, and the test results must be continuously monitored, with modifications confirmed by technical support.
> - If the disk heartbeat timeout is configured too low, it may lead to occasional anomalies such as node eviction and node suicide in environments with poor IO performance.

<span id="fence_type" name="fence_type"></span>

### FENCE_TYPE

*   Parameter Type: Numeric

*   Default Value: 0

*   Value Range/Format: [0|1|2]

*   Parameter Description: The I/O Fencing type of YAC. 0 indicates no configuration, 1 indicates the in-transit I/O protection algorithm, and 2 indicates reservation-based I/O Fencing (supporting SCSI persistent reservation and NVMe reservation). Please refer to [IO Fencing](IO Fencing/00IO Fencing) for the principles, hardware requirements, and configuration methods of different I/O fencing methods, and carefully read and prepare before adjusting this configuration item.

*   Immediate Effect of Changes: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No