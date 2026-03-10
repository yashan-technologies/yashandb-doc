## 通用描述

RESTORE ARCHIVELOG用于从备份恢复指定归档范围的归档日志文件至目标位置。

只能在数据库执行完RESTORE操作且未执行RECOVER的MOUNT状态下执行恢复，且数据库的版本需与生成备份集的数据库版本完全一致。版本校验包括归档文件和数据库的DBID和RESTORE TIME是否一致，归档备份集的RESTORE TIME可查询视图DBA_BACKUP_SET或者使用yasrman list命令查看，待恢复目标数据库的RESTORE TIME可查看V$DATABASE视图。

该语句不适用于存算一体分布式集群部署。

关于备份恢复的详细操作描述请参考[备份恢复](../../全部手册/数据库管理/备份恢复/00备份恢复)。

## 语句定义

**restore archivelog::=**

```ebnf+diagram
syntax::= RESTORE archivelog archivelogRangeSpecifier [DECRYPTION  password] [PARALLELISM integer] FROM (SEARCHDIR|BACKUPSET) "archdirpath" [TO "destpath"]
```

**archivelog_range_specifier::=**

```ebnf+diagram
syntax::= ((
(FROM SCN | SCN BETWEEN interger AND | UNTIL SCN ) interger | 
(FROM SEQUENCE | SEQUENCE BETWEEN interger AND | UNTIL SEQUENCE ) interger [THREAD interger] | 
(FROM TIME | TIME BETWEEN date_string AND | UNTIL TIME ) date_string | ALL))
```
<span id="archivelograngespecifier" name="archivelograngespecifier" class="yaslink"></span>

### 1. archivelogRangeSpecifier

用于指定需恢复的归档日志范围，使用方法同[BACKUP ARCHIVELOG](./BACKUP ARCHIVELOG)。

若范围区间内有指定的归档日志文件不存在（例如文件已被删除、某一序列号对应的归档日志文件暂未生成等）现象，恢复操作会失败并提示错误码YAS-02540。

#### 1.1. FROM

在归档恢复语句中，与SCN、SEQUENCE或TIME配合使用指定为归档日志恢复的起点。

指定FROM指令恢复归档日志时，恢复的终点为当前数据库的日志恢复点的上一个归档文件，具体取值可以查询V$DATABASE视图的RCY_POINT字段中的ASN。

#### 1.2. BETWEEN … AND …

在归档恢复语句中，与SCN、SEQUENCE或TIME配合使用指定归档日志备份的区间。

指定BETWEEN …（起点） AND …（终点）指令恢复归档时，恢复的起点和终点均为指定值，区间范围应为[当前数据库的日志刷盘点ASN,当前数据库的日志恢复点的上一个归档文件]的子集。

#### 1.3. UNTIL

在归档恢复语句中，与SCN、SEQUENCE或TIME配合使用指定为归档日志恢复的终点。

指定UNTIL指令恢复归档日志时，恢复的起点为当前数据库的日志刷盘点ASN，具体取值可以查询V$DATABASE视图的FLUSH_POINT字段中的ASN。

### 2. DECRYPTION

该语句用于指定备份集恢复时解密，此时需要同时指定解密密码。

### 3. PARALLELISM

该语句用于指定多线程恢复的并行度，取值范围为\[1,8\]，省略时默认为2。

### 4. FROM SEARCHDIR/BACKUPSET

该语句指定归档备份集的集合，若目录下存在多个归档备份集的集合，可使用SEARCHDIR关键字指定集合的文件夹（备份集目录的上一层），若只指定单个备份集，使用关键字BACKUPSET即可。

> **Note**: 
>
> 若备份集集合文件夹中各备份集的加密密码不一致，则在恢复过程中会自动跳过密码不一致的备份集。
>
> 若要分别恢复不同解密密码的归档备份集，则需要多次执行符合目标备份集秘钥的恢复语句。

### 5. TO destpath

该语句指定归档的目标恢复路径，需保证该路径存在。省略时默认为ARCHIVE_LOCAL_DEST参数所配置的路径。
