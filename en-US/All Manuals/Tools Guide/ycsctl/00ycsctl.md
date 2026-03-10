*ycsctl* is the YCS management tool for YashanDB. Users can use this tool to perform management operations on YAC/Distributed Cluster, including cluster-level management and node-level management.

- [Cluster Management Commands](Cluster Management Commands): These commands require the YFS service to be started before execution.

- [Node Management Commands](Node Management Commands): These commands require YCS to be started on the node before execution.

Before using *ycsctl*, you must correctly configure the $YASCS_HOME environment variable on each server:

 ```shell
 # The following paths should be replaced with the actual node paths
 # Instance 1-1
 $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-1

 # Instance 1-2
 $ export YASCS_HOME=/data/yashan/yasdb_data/ycs/ce-1-2
 ```
