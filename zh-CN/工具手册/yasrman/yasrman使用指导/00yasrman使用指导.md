## 查看帮助

使用yasrman -h命令可查看帮助信息。

```shell
$ yasrman -h
```

## 命令格式

```shell
yasrman <user>/<password>@<IP>:<port> -c <command> [-D <catalog_path>]
yasrman -c <command> [-D <catalog_path>]
```

**\<user>/\<password>@\<IP>:\<port>**

指向数据库实例（单机部署）或CN节点（存算一体分布式集群部署）的连接信息，支持远程TCP连接和本地UDS连接，IP地址支持IPv4和IPv6。

根据执行的命令不同对数据库用户的权限要求不同：

- 全库RESTORE仅SYS用户才可执行。
- BACKUP、RECOVER、表空间RESTORE需要SYS超级用户或拥有SYSDBA、SYSBACKUP权限的用户才可执行。

> **Note**: 
>
> - BACKUP和RESTORE命令必须指定连接信息才可执行。
> - 其他语句不与数据库交互，不会对用户名、密码和IP进行正确性校验，仅为保持命令格式统一。

**-C \<command>**

-C参数用于指定具体的命令语句，包括：

- [CREATE CATALOG](CREATE CATALOG)：用于创建catalog路径。首次使用yasrman进行备份恢复前，必须使用本命令创建catalog。

- [CONFIGURE与SHOW ALL](CONFIGURE)：CONFIGURE用于创建或更改影响数据库上的yasrman备份、还原的持久配置。SHOW ALL命令可以查看上述配置详细信息。

- [BACKUP](BACKUP)：用于备份主数据库/备数据库、表空间、归档文件。也部分支持共享集群和分布式集群的备份功能。

- [DELETE](DELETE)：用于删除指定的备份集。

- [LIST](LIST)：用于显示yasrman存储库中记录的备份集信息。

- [RECOVER](RECOVER)：用于对表空间对象文件执行一致性恢复。

- [RESTORE](RESTORE)：使用RESTORE命令可基于yasrman生成的备份集进行恢复，包括全库恢复、某一表空间、归档文件的恢复操作。

- [VALIDATE](VALIDATE)：用于检查catalog中记录的备份集文件是否完整，从而确定是否可以用于恢复。

**-D \<catalog_path>**

-D参数用于指定catalog路径，必须指定为绝对路径。-D命令省略时，catalog_path默认为当前路径。

- 当command为CREATE CATALOG时，catalog_path必须为新路径，用于创建catalog。

- 当command为其他时，catalog_path必须为已存在的catalog路径。
