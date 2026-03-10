The CONFIGURE command creates or modifies persistent configurations that affect backups and restorations for the *yasrman* database. The configuration is valid for any *yasrman* session on that database until explicitly cleared or changed.

The SHOW ALL command can be used to view the details of the above configuration.

## CONFIGURE

```ebnf+diagram
syntax::= configure parameter_name parameter_value
```

Executing this command will persist the configured settings into the config.ini file of the catalog. When performing subsequent backups or restorations, if the corresponding configuration is not explicitly specified, the settings from the config.ini file will be used by default.

> **Note**: 
>
> Configuration parameter priority: parameters specified by the backup or restore command > parameters configured by the configure command > default parameters.

### PARALLELISM

```ebnf+diagram
syntax::= configure PARALLELISM (integer|CLEAR)
```

Used to specify the degree of parallelism for multi-threaded backup restoration per node, where integer is described in the developer's manual [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/BACKUP DATABASE) under the PARALLELISM statement.

When specified as CLEAR, the value of the PARALLELISM parameter in the config.ini file is reset to the default value.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure PARALLELISM 3' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure PARALLELISM clear' -D /home/yashan/catalog
```

### COMPRESSION

```ebnf+diagram
syntax::= configure COMPRESSION (ALGORITHM (LZ4|ZSTD|CLEAR)|(LEVEL (LOW|MEDIUM|HIGH|CLEAR)))
```

Specifies the compression algorithm for generating backup sets, with ALGORITHM and LEVEL described in the developer's manual [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/BACKUP DATABASE) under the COMPRESSION statement.

When ALGORITHM is specified as CLEAR, the value of the COMPRESSION_ALGORITHM parameter in the config.ini file is set to NULL, indicating that the compression option is disabled.

When LEVEL is specified as CLEAR, the value of the COMPRESSION_LEVEL parameter in the config.ini file is reset to the default value.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure COMPRESSION ALGORITHM lz4' -D /home/yashan/catalog
$ yasrman sys/********@192.168.1.2:1688 -c 'configure COMPRESSION LEVEL HIGH' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure COMPRESSION ALGORITHM clear' -D /home/yashan/catalog
$ yasrman sys/********@192.168.1.2:1688 -c 'configure COMPRESSION LEVEL clear' -D /home/yashan/catalog
```

### SIZE

```ebnf+diagram
syntax::= configure SECTION SIZE (size_clause|CLEAR)
```

Specifies the slice size of the generated backup set, where size_clause is described in the developer's manual [BACKUP DATABASE](../../../Development Guide/SQL Reference Manual/SQL Statements (yashan Mode)/BACKUP DATABASE) under the SECTION SIZE statement.

When specified as CLEAR, the value of the SECTION_SIZE parameter in the config.ini file is reset to the default value.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure section size 128M' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure section size clear' -D /home/yashan/catalog
```

### DSTB\_NODES

```ebnf+diagram
syntax::= configure DSTB_NODES "dstb_nodes_file_path"
```

Specifies the path to the configuration file for IP transformation in ISC Distributed Cluster Deployment. The format of the configuration file is as follows:

```shell
nodeCount=5
node_id=1-1, node_type=MN, listen_addr=127.0.0.1:11678, din_addr=127.0.0.1:11679, replica_addr=127.0.0.1:11680, data_path='/data/shm/databak/mn-1-1', hostname=host0001
node_id=3-1, node_type=DN, listen_addr=127.0.0.1:11698, din_addr=127.0.0.1:11699, replica_addr=127.0.0.1:11700, data_path='/data/shm/databak/dn-3-1', hostname=host0001
node_id=5-1, node_type=DN, listen_addr=127.0.0.1:11704, din_addr=127.0.0.1:11705, replica_addr=127.0.0.1:11706, data_path='/data/shm/databak/dn-5-1', hostname=host0001
node_id=2-1, node_type=CN, listen_addr=127.0.0.1:11688, din_addr=127.0.0.1:11689, replica_addr=127.0.0.1:11690, data_path='/data/shm/databak/cn-2-1', hostname=host0001
node_id=4-1, node_type=DN, listen_addr=127.0.0.1:11701, din_addr=127.0.0.1:11702, replica_addr=127.0.0.1:11703, data_path='/data/shm/databak/dn-4-1', hostname=host0001
```

The first line indicates the total number of nodes in the ISC Distributed Cluster Deployment, and the following lines represent the NODEID and type of each node. The node types, IDs, and counts in the configuration file must match exactly with those during cluster backup. The listen_addr field indicates the IP and port for each node, the din_addr indicates the internal communication IP and port in the ISC Distributed Cluster Deployment, the replica_addr indicates the primary/standby communication IP within the same group, data_path is the path of the new cluster deployment for $YASDATA_DATA, and hostname is the internal server address where the cluster nodes are deployed.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure dstb_nodes '/home/yashan/catalog/dstb_nodes_file'' -D /home/yashan/catalog
```

If there are no node IP changes in the ISC Distributed Cluster Deployment, the following command can be executed to clear the node IP modification configuration.

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure dstb_nodes clear' -D /home/yashan/catalog
```

### DEST

```ebnf+diagram
syntax::= configure DEST (CLIENT|SERVER|CLEAR)
```

Specifies the storage location of the backup set, with CLIENT indicating storage on the tool side (the backup set will be saved on the device where *yasrman* is located), and SERVER indicating backup on the database server side (the backup set will be saved on the database's server). If unspecified, the default is SERVER.

When specified as CLEAR, the value of the DEST parameter in the config.ini file is reset to the default value.

> **Note**: 
>
> Under YAC Deployment, if the format option specifies a YFS path, for example: +DG0/bak1, the dest option must be specified as SERVER.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure DEST SERVER' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure DEST clear' -D /home/yashan/catalog
```

## SHOW ALL

```ebnf+diagram
syntax::= SHOW ALL
```

Executing this command will list the backup parameters already configured in the config.ini. However, since these parameters have a lower priority than those explicitly specified in backup and restoration statements, the parameters shown in the command's query results may not match the actual parameters used during backup execution.

***Example***

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'show all' -D /home/yashan/catalog
+---------------------------+-----------+----------------+
|           NAME            |  DEFAULT  |      VALUE     |
+---------------------------+-----------+----------------+
| PARALLELISM               | 2         | 2              |
| SECTION SIZE              | 134217728 | 134217728      |
| COMPRESSION ALGORITHM     | NONE      | NONE           |
| COMPRESSION LEVEL         | LOW       | LOW            |
| DSTB NODES                | ''        | /home/yashan/catalog/dstb_nodes_file |
+---------------------------+-----------+----------------+
```
