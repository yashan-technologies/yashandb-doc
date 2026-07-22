DELETE用于在单机部署中的主库或存算一体分布式集群部署处于正常运行状态时删除指定的备份集。

```ebnf
= DELETE BACKUPSET [IF EXISTS] TAG tag_name.
```
指定的tag_name不存在时，本命令报错。

指定IF EXISTS表示若备份集存在则尝试删除，不论tag_name是否存在都返回删除成功。

示例

```shell
$ yasrman sys/********@192.168.1.2:1688 -c "DELETE BACKUPSET tag 'full_1'" -D /home/yashan/catalog
```

> **Note**: 
>
> 如果删除过程中断，可能出现部分节点的备份集已删除，但是剩余节点备份集和TAG还存在的情况，此时可以通过重新执行删除命令，继续删除。
> 
> 如果catalog中存在备份集元信息，但数据库中没有记录元信息，不指定IF EXISTS删除备份集可能报错，请使用IF EXISTS来删除备份集。如果catalog文件被误删或者catalog和目标数据库备份集系统表不匹配，可使用该命令并且附加IF EXISTS来删除系统表信息。
> 
> 仅使用该命令才可同时删除catalog和系统表中的备份集信息。
