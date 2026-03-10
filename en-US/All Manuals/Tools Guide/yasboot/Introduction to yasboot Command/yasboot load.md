## load

This command is used to split large CSV files and import the split smaller files into the physical tables of YashanDB. The functionality for both file splitting and importing in this module is provided by *yasldr*.

|Option |Description |
| --------------------------- | ------------------------ |
| *-c, --cluster*              | Cluster name of YashanDB (mandatory parameter)                                 |
| *-b, --batch-size*           | Number of rows per batch of CSV data, range [1,65535]                           |
| *-m, --mode*                 | Import mode, including BASIC and BATCH modes                               |
| *-sm, --split-mode*          | Split mode, including PART, NODE, and NODEPART modes, introduced below, default is PART. The `PART` split mode must be used for importing distributed duplicated tables. |
| *-f, --control-file*         | Path to `control_file`, which must contain `run_level=SPLIT` or `run_level=SPLIT_to_part`   |
| *-j, --part-job*             | Number of *yasldr* instances to start in parallel when importing partition data to nodes                   |
| *-n, --no-load*              | Only split, do not import                                |
| *-s, --split-file-directory* | Path where the split files are located, indicating the data to be imported into the database |
| *-u, --username*  | Specify the database user. If not specified, the default user sys is used |
| *-p, --password*  | Password for the database user <br/>If the `sys` user is used and [OS authentication](../../../Product Security/Identity Identification and Authentication/OS Authentication/00OS Authentication) (enabled by default after installation) is activated, no password needs to be specified   |
| *-tl, --to-local*            | Package the split files to the nodes and perform local import      |
| *--param*                    | Command line parameters passed to *yasldr*                      |
| *--gz*                       | Compress the packaged files                   |
| *--delete*                   | Delete CSV files in the directory where split files are stored                      |
| *--host-id*                  | Server ID where the CSV file is located before splitting, can be queried using `yasboot cluster status`, defaults to the current server ID  |
| *-w, --nowait*               | Do not wait for the execution command result after running                           |
| *-d, --child*                | Display task and sub-task information                            |
| *--disable*                  | Suppress the display of the task progress bar                              |

> **Note**:
>
> The directory for storing split files is specified by the `directory_clause` in the `control_file`. If not specified, it defaults to the directory where the first file in infile is located. For detailed explanation of `directory_clause`, please refer to [yasldr](../../yasldr/User Guide for yasldr).

**Split Modes**

The `yasboot load` command supports the following three split modes to split CSV into partition data:

|Split Mode |Description |
| -------- | ------------------------ |
| PART       | <br/>\*  Standalone Deployment : <br/> &nbsp; &nbsp;- Splitting and importing both connect to the primary database. *  ISC Distributed Cluster Deployment :  <br/>  &nbsp;&nbsp; - Connect to the DN primary node to split into partition data.<br/> &nbsp; &nbsp;- Import partition data via connection to the CN node. |
| NODE       | ISC Distributed Cluster Deployment : <br/> &nbsp; &nbsp;- Connect to the CN node to split into node data; <br/> &nbsp; &nbsp;- Connect to the DN primary node to import node data into each respective node. |
| NODEPART   | ISC Distributed Cluster Deployment : <br/> &nbsp; &nbsp;- Connect to the CN node to split into node partition data;<br/> &nbsp; &nbsp;- Connect to the DN primary node to import node partition data into each respective node. |

***Example***

```shell
# One-click split and import CSV data (the --host-id parameter not specified, defaults to CSV file on the current server)
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm PART --param "BATCH_SIZE=2048 CONN_POOL_SIZE=10"

# One-click split and import CSV data on server host0002
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART --host-id host0002

# One-click split, package and transmit to node machine, perform local import of CSV data
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -tl

# One-click split CSV file
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -n

# One-click split CSV file and package for transmission to node
$ yasboot load -c yashandb -f /var/database/yashan/load/yashanload.ctl -sm NODEPART -n -tl

# One-click import of already split CSV data (precondition: the CSV file has been split with one-click command)
$ yasboot load -c yashandb -s /var/database/yashan/load/splitFileDirectory
```
