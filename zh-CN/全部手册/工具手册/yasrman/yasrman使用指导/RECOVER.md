RECOVER用于对表空间对象文件执行一致性恢复。

```ebnf+diagram
syntax::= RECOVER TABLESPACE ((TBS_NAME) {"," (TBS_NAME)}) [DECRYPTION password] [MAXSIZE integer]
```

RECOVER操作仅适用于单机部署。

执行RECOVER操作要求在数据库处于MOUNT或OPEN状态，且目标表空间的状态为OFFLINE，该表空间下的数据文件为RECOVER状态。

RECOVER操作与备份操作不能并行。

RECOVER执行过程中会自动搜索可用的归档文件，搜索范围包括数据库默认的归档文件夹和归档备份集。若没有符合条件的归档则会导致RECOVER操作失败。

### TABLESPACE

指定需要执行RECOVER操作的表空间，可对指定的目标表空间恢复至与所属数据库一致状态。

TBS_NAME为需要RECOVER的表空间名，可指定多个。

### DECRYPTION

指定恢复归档所需要的密码，如果RECOVER所需的归档备份文件已被加密，需要指定该密码恢复目标归档文件，并RECOVER应用至目标表空间。

### MAXSIZE

指定需要恢复的最大归档上限大小，默认值为无限大，取值范围为[128M,32T]。

示例（单机部署）
```shell
$ yasrman sys/********@192.168.1.2:1688 -c "recover tablespace spc_test maxsize 1280M " -D /home/yashan/catalog

```
