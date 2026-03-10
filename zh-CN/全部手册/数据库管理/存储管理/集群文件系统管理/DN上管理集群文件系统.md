YashanDB分布式集群部署中的存储网络由DN提供智能分布式存储服务。登录到DN服务器上时，可通过yfscmd命令查询当前服务器上的文件系统信息。

![](./image/DN.png)

## 查询磁盘管理服务

使用以下命令查询DN上的磁盘管理服务是否正常。

```bash
$ ps -aux | grep yasfs
user   11898  1.0  0.3 259456 112324 pts/4   Sl+  15:49   0:00 yasfs -D /YOUR/SRV/HOME -m
```

注意`/YOUR/SRV/HOME`与实际部署环境有关，请以实际情况为准。

## 查询文件系统

DN与CN采用统一的文件服务终端yfscmd，yfscmd可以交互式或非交互式启动（参考[yfscmd命令指导](../../../工具手册/yfscmd/00yfscmd)）。

DN上可通过yfscmd的`show disk`命令查看本节点上的磁盘信息。

1. 以交互模式登录DN的磁盘监控服务，在yfscmd中输入`show disk`命令显示当前节点所有磁盘：

```bash
$ yfscmd -D /YOUR/SRV/HOME
server DISK MONITOR mode
YAS File System CMD Enterprise Edition Debug 23.4.3.102 x86_64 8451cb6
try help or ?.

YFSCMD >  show disk
id name     status   fgid dgid au_size  au_count path
0  SYSTEM_0 NORMAL   0    0    1.00MB   1024     /DISKS/disk1
2  DG0_0    NORMAL   0    1    32.00MB  640      /DISKS/disk2
```

2. 以非交互模式直接查询当前节点所有磁盘：

```bash
$ yfscmd -D /YOUR/SRV/HOME show disk
server DISK MONITOR mode
id name     status   fgid dgid au_size  au_count path
0  SYSTEM_0 NORMAL   0    0    1.00MB   1024     /DISKS/disk1
2  DG0_0    NORMAL   0    1    32.00MB  640      /DISKS/disk2
```