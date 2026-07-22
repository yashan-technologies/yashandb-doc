通用描述
----

RESTORE DATABASE用于从备份集还原出数据库文件，恢复到备份时的状态。

如果备份时全库闪回处于开启状态，则RESTORE DATABASE完成后仍会保持开启状态，但相关的资源信息以及内容都会被重置。

只能在数据库处于NOMOUNT状态且旧的数据文件已被删除时执行恢复，且数据库的版本需与生成备份集的数据库版本完全一致。

成功恢复后数据库变更为MOUNT状态，此时还需执行[RECOVER](./RECOVER DATABASE)操作，以使系统根据日志还原到指定时间点。

该语句不适用于存算一体分布式集群部署。

关于备份恢复的详细操作描述请参考[备份与恢复](../../../数据库管理/备份与恢复/00备份与恢复)。

> **Note**: 
>
> 若备份集中存在加密对象，恢复前需要先打开对应的密钥钱包文件，再进行恢复操作。

语句定义
----

**restore database::=**

```ebnf
= RESTORE DATABASE [INCREMENTAL [NOREDO]] [DECRYPTION  password] FROM backup_path [PARALLELISM integer].
```

### INCREMENTAL [NOREDO]

该语句用于指定增量RESTORE恢复数据库，仅在指定INCREMENTAL字段后才可使用NOREDO字段，指定NOREDO字段可在恢复时跳过恢复redo和归档文件，提升RESTORE效率。

### DECRYPTION

该语句用于指定备份集恢复时解密，此时需要同时指定解密密码。

### backup\_path

该语句用于指定用于恢复的备份集名称。

### PARALLELISM

该语句用于指定多线程恢复的并行度，该值范围为[1,16]，省略时默认为2。

示例（单机/共享集群/分布式集群部署）

```sql
RESTORE DATABASE DECRYPTION 12345 FROM 'backup';
```
