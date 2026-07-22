## Overview

The in-transit I/O protection algorithm is a hardware-independent, general-purpose I/O fencing method provided by YCS. If the shared storage device used for deploying the YAC/distributed cluster does not meet the requirements for Reservation-based I/O Fencing, the in-transit I/O protection algorithm is used by default.

YAC/distributed cluster database instance and the cluster file system will persistently record the in-transit I/O count initiated by this instance to the voting disk according to a certain strategy. If the corresponding server is evicted from the cluster, the cluster database instance will actively block new I/O requests and safely exit after processing the old in-transit I/O.

If the primary server detects that the database instance or cluster file system of the evicted server has in-transit I/O before updating the cluster membership, it will wait for a safe exit or until a timeout occurs.

Compared to I/O Fencing methods that rely on hardware capabilities, the in-transit I/O protection algorithm will to some extent extend the cluster RTO. 

> **Caution**:
>
> The in-transit I/O protection algorithm cannot intercept all in-transit I/O operations. Using this method carries a risk of split-brain. **It is recommended to use** [Reservation-based IO Fencing](./Reservation-based IO Fencing).


## Hardware and Environment Requirements

No hardware devices or configurations are required.

When storage device I/O performance is poor or I/O stalling occurs frequently, it is necessary to increase the DISK_HB_KEEP_ALIVE parameter configuration value for YAC/distributed cluster to ensure the reliability of the algorithm. The DISK_HB_KEEP_ALIVE parameter configuration must be greater than the maximum theoretical I/O delay of the shared storage device.

## Configuration and Usage

When deploying YAC/distributed cluster, *yasboot* will automatically select the optimal fence type, and manual configuration is generally not required.

> **Caution**:
>
> The following operations will stop and restart the database cluster. Please confirm in advance that the business has stopped or can accept the stop.


1. Confirm that the business has stopped or can tolerate being stopped, and then stop the database cluster.

    ```shell
    $ yasboot cluster stop -c yashandb
    ```

2. Start the yasfs service.

    ```shell
    $ yasfs &
    ```

3. Execute the [ycsctl set_ycr](../../../Tools Guide/ycsctl/User Guide for ycsctl/Cluster Configuration Commands) command to configure the fence type.

    ```shell
    ycsctl set_ycr FENCE_TYPE 1
    ```

4. Stop the yasfs service.

    ```shell
    $ yfscmd exec "shutdown abort"
    ```

5. Start the database cluster.

    ```shell
    $ yasboot cluster start -c yashandb
    ```

6. Check and start the [YCSRA process](../../../Tools Guide/ycsrootagent).

    ```shell
    $ top -c | grep YCSRA

    # If it doesnot exist, you need to start it manually
    $ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
    YCSRA instance start successfully.
    ```

7. Query the fence status.

    > **Note**:
    >
    > The ycsctl show fence command requires the [YCSRA process](../../../Tools Guide/ycsrootagent) to be online to return results.

    ```shell
    $ ycsctl show fence
    ```

## FAQs

### Self-fencing of Cluster Database

If the alarm log shows a YcsDbFenced alarm event, it may indicate that YAC/distributed cluster database instance blocked I/O requests after the server was evicted. Please refer to the Alarm Event Description for troubleshooting and resolution.