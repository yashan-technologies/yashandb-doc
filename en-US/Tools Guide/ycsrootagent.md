*ycsrootagent* is the privilege operation agent for YCS, mainly running as a background process to provide services for YCS, supporting command line interfaces for starting and stopping YCSRA services, SCSI I/O Fencing, and disk I/O testing.

>**Note**:
>
> All interfaces provided by ycsrootagent must be executed under root (or sudo).

## View Help

Use the command ycsrootagent -h to view help information.

```shell
$ ycsrootagent -h
```

## ycsrootagent {start|stop}

This command is used to manually start or stop the YCSRA background service process. The YCSRA process provides critical services for YCS and should not be easily stopped manually to ensure the normal operation of the cluster.

If the cluster is deployed through *yasboot*, it will automatically start YCSRA when the user provides sudo credentials or configures sudo without a password. If the YCSRA process is abnormal, please manually start YCSRA according to the example to restore services.

|Option |Description |
| ------------------ | ---------------------------------------------------- |
| *-H* | The home directory where YCSRA runs, which is the home directory of the YCS it serves. It must be an absolute path and cannot be omitted. |

***Example***: Taking the home directory as /data/yashan/yasdb_data/ycs/ce-1-1 as an example, please replace it with the actual path during the actual operation.

```shell
$ sudo ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
YCSRA instance start successfully.

$ sudo ycsrootagent stop -H /data/yashan/yasdb_data/ycs/ce-1-1

# Example of insufficient privileges error
$ ycsrootagent start -H /data/yashan/yasdb_data/ycs/ce-1-1&
YAS-00521 process permission is insufficient.
```

<span id="scsi_clear" name="scsi_clear"></span>

## ycsrootagent resv {allow|ban|clear}

In scenarios where automatic detection of whether storage devices support reservation-based IO fencing is needed, *yasboot* will automatically call the fenceResvCheck script to invoke relevant subcommands for this command. For manual detection, please directly execute the [fenceResvCheck script](../Database Administration/Cluster Management/IO Fencing/Reservation-based IO Fencing.md#fenceResvCheck_usage), **it is not recommended for users to execute this command directly**.

The clear subcommand is used to clean up residual persistent reservation information on storage devices and should be **executed cautiously as needed** only upon receiving a clear cleanup prompt or YAS-00341 error code.

>**Warn**:
>
> This command will change the access rules and accessibility of the storage devices. Incorrect usage will have a serious impact on the operation of the cluster.

|Option |Description |
| ------------------ | ---------------------------------------------------- |
| *-d* | The name of the single disk device being operated, it must be an absolute path and cannot be omitted. Maximum 31 characters. |
| *-a* | If this option is included, it indicates that the command is being executed on a server assisting with the test. |
| *-f* | Valid only for the clear subcommand. If this option is included, it will skip the risk prompt and force execution. |
| *--all* | Valid only for the clear subcommand. If this option is included, it will clean up all disks that have performed persistent reservation operations before. |

## ycsrootagent pingdisk

In scenarios where automatic detection of whether storage devices support SCSI I/O Fencing is needed, *yasboot* will automatically call the fenceResvCheck script to invoke this command. For manual detection, please directly execute the [fenceResvCheck script](../Database Administration/Cluster Management/IO Fencing/Reservation-based IO Fencing.md#fenceResvCheck_usage), **it is not recommended for users to execute this command directly**.

>**Warn**:
>
> This command will read and write specific areas of the device multiple times. Incorrect usage will affect performance.

|Option |Description |
| ------------------ | ---------------------------------------------------- |
| *-d* | The name of the single disk device being operated, it must be an absolute path and cannot be omitted. Maximum 31 characters. |

## ycsrootagent vip clear

This command is used to clean up all VIPs on this server.

|Option |Description |
| ------------------ | ---------------------------------------------------- |
| *-d* | If this option is included, it will skip the risk prompt and force execution. |

## ycsrootagent network verify

This command is used to verify accessibility to specific network and corresponding network interface functioning properly or not on this server.

|Option |Description |
| ------------------ | ---------------------------------------------------- |
| *-subnet* | The subnet address and network mask which defines a subnet, in format of subnet/netmask[/interface]. |