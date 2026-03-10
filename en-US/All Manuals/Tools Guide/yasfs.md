*yasfs* is a single-instance YFS service program that provides file system services during YAC/Distributed Cluster installation, offline upgrades, and offline modifications of cluster configurations.

>**Warn**:
>
> Only allows starting single-instance yasfs service when using ycsctl for offline cluster configuration modification or offline PDB addition. After ycsctl offline operations are completed, the yasfs service must be stopped before starting the cluster.
>
> While the single-instance *yasfs* service is running, other nodes in the same YAC/Distributed Cluster must not start the YCS or *yasfs* services, **otherwise, it may cause metadata corruption or data loss**.

## View Help

Use `yasfs -h` to view help information.

```shell
$ yasfs -h
yasfs: option requires an argument -- 'h'

Usage:
  yasfs [OPTION]

Options:
  -D  home path, use YASCS_HOME instead if this option is not given
  -u  upgrade mode
```

## Starting and Stopping yasfs

*yasfs* can be started in two ways:

```shell
# When executing, please replace the example path with the actual path
Method 1: Set the YASCS_HOME environment variable and then run yasfs
$ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1; 
$ yasfs &

Method 2: Specify YASCS_HOME using the -D option
$ yasfs -D /data/yashan/yasdb_data/ycs/ce-1-1 &
```

*yasfs* can be stopped in two ways:

```bash
Method 1: Set the YASCS_HOME environment variable and execute shutdown
$ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1
$ yfscmd exec "shutdown abort"

Method 2: Specify YASCS_HOME using the -D option
$ yfscmd -D /data/yashan/yasdb_data/ycs/ce-1-1 exec "shutdown abort"
```
