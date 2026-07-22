##  nvme rebuild

This command allows for manual reconstruction of the NVMe disk configuration of the distributed cluster.

|Option   |Meaning                                                   |
|-----------------|------------------------------------------------------------------|
| *-c, --cluster* | The cluster name of YashanDB (mandatory parameter)               |
| *--cn*          | Whether to rebuild the NVMe configuration related to the CN node |
| *--dn*          | Whether to rebuild the NVMe configuration related to the DN node |
| *-h,--help*          | View help information for the current command  |

***Example***

```shell
$ ./bin/yasboot nvme rebuild -c yashandb --cn --dn
```

**Command Result**

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
