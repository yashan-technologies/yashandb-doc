##  yfs config

This command can be used to view the yfs configuration information for the corresponding node.

|Option |Meaning |
| --------------- | ----------------------------------------------------------- |
| *-c, --cluster*   | The cluster name of YashanDB (mandatory parameter)          |
| *-n, --node-id*   | Node ID (e.g., 1-1, can be viewed through the `yasboot cluster status` command, without the colon and the following numbers) (mandatory parameter) |
| *-q, --query*     | Query a single or multiple parameters from the configuration (fuzzy matching is allowed) |
| *-a, --all*       | Query all parameters                                          |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ ./bin/yasboot yfs config show -c yashandb -n 1-1
```

**Command Result**

```text
+-------------------------------------------------------+
| parameters      | current_value                       |
+-------------------------------------------------------+
| SHM_POOL_SIZE   | 2G                                  |
+-----------------+-------------------------------------+
| SYS_AREA_SIZE   | 1G                                  |
+-----------------+-------------------------------------+
| YFS_PACKET_SIZE | 1M                                  |
+-----------------+-------------------------------------+
| _CLUSTER_ID     | 66ebdea5886da3c97007c4c24aeef5a9    |
+-----------------+-------------------------------------+
| YFS_DISKSTRING  | /dev/yfs                            |
+-----------------+-------------------------------------+
```
