通用描述
----

RECOVER DATABASE用于从备份集和归档日志/在线日志还原数据库到指定时间点的状态，其中：

   RECOVER DATABASE (FROM)=完全恢复，即尽可能回放所有redo，恢复到最新状态。

   RECOVER DATABASE UNTIL ...=不完全恢复，即恢复到指定的目标点就停止。

只能在数据库状态为MOUNT时执行RECOVER。

通过备份集恢复数据库时，在RECOVER之前必须先执行[RESTORE](./RESTORE DATABASE)将数据库恢复到备份集的状态。执行PITR恢复时，RECOVER操作要求在备份集生成时间之后且指定时间点之前的归档日志必须存在。

执行不完全恢复后，需执行ALTER DATABASE OPEN RESETLOGS语句开启数据库并重置redo时间线。

该语句不适用于存算一体分布式集群部署。

关于备份恢复的详细操作描述请参考[备份与恢复](../../全部手册/数据库管理/备份与恢复/00备份与恢复)。

语句定义
----

**recover database::=**

```ebnf+diagram
syntax::= RECOVER DATABASE [(UNTIL (TIME date|SCN scn|CANCEL))|(FROM SEQUENCE integer)]
```

### 1. UNTIL TIME 

该语句用于将数据库恢复到此指定的时间点，date格式需与DATE_FORMAT参数定义的格式一致。

指定的时间点必须在备份集生成时间之后。

> **Note**: 
>
> 最小时间点是保证数据库恢复到一致性的时间点，该时间点对应与备份完datafile，开始备份归档时的SCN对应的时间点，略小于备份完成时间。
>
> 如果归档日志不够，或者指定的时间点过大，回放完毕后也达不到指定的时间点，数据库会报错，用户可以查询V$DATABASE的current_scn来查看实际回放进度。如果有可用的归档日志副本，可以注册归档，重新执行restore操作。如果没有多余的归档，当前时间点是唯一的选择，继续执行resetlogs操作。

示例（单机/共享集群/分布式集群部署）

```sql
--启动数据库到NOMOUNT状态，然后执行restore操作
RESTORE DATABASE FROM 'backup';
 
--RESTORE操作成功后数据库启动到MOUNT状态
RECOVER DATABASE UNTIL TIME TO_DATE('2021-11-25 18:55:00','yyyy-mm-dd hh24:mi:ss');
 
--打开数据库
ALTER DATABASE OPEN RESETLOGS;
```

### 2. UNTIL SCN 

该语句用于将数据库恢复到此指定的SCN号。指定的SCN号必须大于备份集中记录的SCN号。

> **Note**: 
>
> 最小SCN是保证数据库恢复到一致性的SCN，该SCN就是备份完datafile，开始备份归档时的SCN，可能略小于备份完成时的SCN。
>
> 如果归档日志不够，或者指定的SCN过大，回放完毕后也达不到指定的SCN，数据库会报错，用户可以查询V$DATABASE的current_scn来查看实际回放进度。如果有可用的归档日志副本，可以注册归档，重新执行restore操作。如果没有多余的归档，当前时间点是唯一的选择，继续执行resetlogs操作。

### 3. UNTIL CANCEL 

该语句用于数据库正常启动失败，无法恢复到一致性状态，没有其他恢复方式的情况下，可以使用该语句强制启动。执行该语句后，执行OPEN命令需要带RESETLOGS。

> **Warning**: 
>
> 该语句为高危操作，使用该语句强制启动数据库后，数据库可能会产生各种无法预料的故障，例如：宕机、事务不一致等。
>
> 当数据库无法恢复到一致性状态时，优先考虑其他恢复方式，最后考虑该恢复方式。
>
> 使用该语句强制启动数据库后，建议导出必要的数据后重建数据库，不要继续使用原来的数据库，以防出现未知的故障。

示例（单机/共享集群/分布式集群部署）

```sql
--先将数据库启动到MOUNT，然后执行以下语句，强制启动
RECOVER DATABASE UNTIL CANCEL;

--打开数据库
ALTER DATABASE OPEN RESETLOGS;
```

### 4. FROM SEQUENCE 

该语句用于数据库从指定的日志序列号开始恢复，指定的日志序列号不能大于当前数据库的恢复开始点（对应于V$DATABASE的RCY_POINT），需保证指定序列号之后的日志存在。
