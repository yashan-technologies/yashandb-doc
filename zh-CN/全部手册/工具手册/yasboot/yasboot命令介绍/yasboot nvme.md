##  nvme rebuild

本命令可以手动重建分布式集群的NVMe磁盘配置。

| 选项| 含义|
|-----------------|------------------------------------------------------------------|
| *-c, --cluster* | YashanDB的集群名（必传参数）                                               |
| *--cn*          | 是否重建cn节点的相关NVMe相关配置                                              |
| *--dn*          | 是否重建dn节点的相关NVMe相关配置                                              |

示例

```shell
$ ./bin/yasboot nvme rebuild -c yashandb --cn --dn
```

**命令结果**

```text
nvme-cli is installed, pass...
modprobe nvmet succeed
modprobe nvmet_rdma succeed
modprobe nvme_rdma succeed
modprobe nvme_fabrics succeed

Discovery Log Number of Records 1, Generation counter 119
=====Discovery Log Entry 0======
trtype:  rdma
adrfam:  ipv4
subtype: nvme subsystem
treq:    not specified
portid:  1
trsvcid: 1700
subnqn:  nqn.yashandb.com:NVMf:uuid:0d5fc2eb65ca7f1607d1c0411856bafe
traddr:  192.168.1.2
rdma_prtype: not specified
rdma_qptype: connected
rdma_cms:    rdma-cm
rdma_pkey: 0x0000
connect to nqn.yashandb.com:NVMf:uuid:0d5fc2eb65ca7f1607d1c0411856bafe succeed
/dev/nvme2n1
```
