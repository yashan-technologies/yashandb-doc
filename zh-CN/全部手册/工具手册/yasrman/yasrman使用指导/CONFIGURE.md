CONFIGURE命令创建或更改影响数据库上的yasrman备份、还原的持久配置。配置对该数据库上的任何yasrman会话都有效，直到明确清除或更改配置为止。

SHOW ALL命令可以查看上述配置详细信息。

## CONFIGURE

```ebnf+diagram
syntax::= configure parameter_name parameter_value
```

执行本命令将所配置的内容持久化到catalog的config.ini文件。后续执行备份或恢复时，若未显式指定相应配置，将默认调用config.ini文件中的配置。

> **Note**: 
>
> 配置参数命令优先级：backup或restore命令指定的参数 > configure命令配置的参数 > 默认参数。

### PARALLELISM

```ebnf+diagram
syntax::= configure PARALLELISM (integer|CLEAR)
```

用于指定每个节点执行多线程备份恢复的并行度，其中integer见开发手册[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)中PARALLELISM语句描述。

指定为CLEAR时，config.ini文件的PARALLELISM参数值被置为默认值。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure PARALLELISM 3' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure PARALLELISM clear' -D /home/yashan/catalog
```

### COMPRESSION

```ebnf+diagram
syntax::= configure COMPRESSION (ALGORITHM (LZ4|ZSTD|CLEAR)|(LEVEL (LOW|MEDIUM|HIGH|CLEAR)))
```

指定生成备份集的压缩算法，ALGORITHM及LEVEL同开发手册[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)中COMPRESSION语句描述。

将ALGORITHM指定为CLEAR时，config.ini文件的COMPRESSION_ALGORITHM参数值被置为NULL，表示关闭压缩选项。

将LEVEL指定为CLEAR时，config.ini文件的COMPRESSION_LEVEL参数值被置为默认值。

示例

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

指定生成备份集的切片大小，size_clause同开发手册[BACKUP DATABASE](../../../开发手册/SQL参考手册/SQL语句/BACKUP DATABASE)中SECTION SIZE语句描述。

指定为CLEAR时，config.ini文件的SECTION_SIZE参数值被置为默认值。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure section size 128M' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure section size clear' -D /home/yashan/catalog
```

### DSTB\_NODES

```ebnf+diagram
syntax::= configure DSTB_NODES "dstb_nodes_file_path"
```

指定存算一体分布式集群部署中IP转换的配置文件路径，配置文件格式如下所示：

```shell
nodeCount=5
node_id=1-1, node_type=MN, listen_addr=127.0.0.1:11678, din_addr=127.0.0.1:11679, replica_addr=127.0.0.1:11680, data_path='/data/shm/databak/mn-1-1', hostname=host0001
node_id=3-1, node_type=DN, listen_addr=127.0.0.1:11698, din_addr=127.0.0.1:11699, replica_addr=127.0.0.1:11700, data_path='/data/shm/databak/dn-3-1', hostname=host0001
node_id=5-1, node_type=DN, listen_addr=127.0.0.1:11704, din_addr=127.0.0.1:11705, replica_addr=127.0.0.1:11706, data_path='/data/shm/databak/dn-5-1', hostname=host0001
node_id=2-1, node_type=CN, listen_addr=127.0.0.1:11688, din_addr=127.0.0.1:11689, replica_addr=127.0.0.1:11690, data_path='/data/shm/databak/cn-2-1', hostname=host0001
node_id=4-1, node_type=DN, listen_addr=127.0.0.1:11701, din_addr=127.0.0.1:11702, replica_addr=127.0.0.1:11703, data_path='/data/shm/databak/dn-4-1', hostname=host0001
```

其中首行为存算一体分布式集群部署中所有节点个数，从第二行开始表示每个节点的NODEID和该节点的类型，且配置文件的节点类型、节点ID、节点个数必须和集群备份时保持完全一致。listen_addr字段表示更新后各个节点监听的IP和端口号，din_addr为存算一体分布式集群部署中数据库内部通信IP和端口，replica_addr为同一组内节点的主备的通信IP，data_path为新集群部署的$YASDATA_DATA的路径，hostname为集群节点部署的内部服务器地址。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure dstb_nodes '/home/yashan/catalog/dstb_nodes_file'' -D /home/yashan/catalog
```

若存算一体分布式集群部署中未发生节点IP变更，执行以下命令可清理掉节点IP修改配置。

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure dstb_nodes clear' -D /home/yashan/catalog
```

### DEST

```ebnf+diagram
syntax::= configure DEST (CLIENT|SERVER|CLEAR)
```

指定备份集的存储位置，CLIENT表示存储到工具端（备份集会保存在yasrman所在设备），SERVER表示备份到数据库服务端（备份集会保存在数据库所在服务器）。不指定时默认为SERVER。

指定为CLEAR时，config.ini文件的DEST参数值被置为默认值。

> **Note**: 
>
> 共享集群环境下，若format选项指定了YFS路径，例如：+DG0/bak1，则dest选项必须指定为SERVER。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c 'configure DEST SERVER' -D /home/yashan/catalog

$ yasrman sys/********@192.168.1.2:1688 -c 'configure DEST clear' -D /home/yashan/catalog
```

## SHOW ALL

```ebnf+diagram
syntax::= SHOW ALL
```

执行本命令将列出在config.ini中已经配置的备份参数。但由于此类参数的优先级低于备份恢复语句中的显式指定，本命令的查询结果所示参数可能与实际执行备份时使用的参数不一致。

示例

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
