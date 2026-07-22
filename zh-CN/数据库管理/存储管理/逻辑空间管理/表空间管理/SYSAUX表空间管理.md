SYSAUX表空间为SYSTEM表空间的辅助表空间，是YashanDB许多特性（如快照信息）的默认表空间。其总大小由这些特性组件占用的空间决定，组件占用的空间取决于特性正在使用的功能，以及数据库工作负载的状况。

SYSAUX表空间容纳多个组件所需空间，可以减少数据库所需的表空间数量，以及减少SYSTEM表空间上的负载。

YashanDB不允许删除或重命名SYSAUX表空间，当SYSAUX表空间由于异常变得不可用时，核心数据库功能仍保持运行，但使用SYSAUX表空间的相关功能可能会失败或者受限。

如在默认安装时未对SYSAUX表空间做任何配置，随着时间的推移，该表空间会越来越大，建议数据库管理员进行日常监控和定期清理。

**为SYSAUX表空间添加数据文件**

> **Note**: 
>
> 指定数据文件路径时，单机部署中可按绝对或相对路径指定，共享集群/分布式集群部署中按绝对路径指定且只能为YFS路径，存算一体分布式集群部署中只允许按相对路径指定。

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE sysaux ADD DATAFILE '?/dbfiles/sysaux01' SIZE 10G AUTOEXTEND OFF;
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE sysaux ADD DATAFILE '+DG0/dbfiles/sysaux01' SIZE 10G AUTOEXTEND OFF;
```

**为SYSAUX表空间删除数据文件**

示例（单机部署）

```sql
ALTER TABLESPACE sysaux DROP DATAFILE '?/dbfiles/sysaux01';
```

示例（共享集群/分布式集群部署）

```sql
ALTER TABLESPACE sysaux DROP DATAFILE '+DG0/dbfiles/sysaux01';
```

**为SYSAUX表空间调整数据文件大小**


此功能不适用于存算一体分布式集群部署。


示例（单机部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/sysaux01' RESIZE 20G;
```

示例（共享集群/分布式集群部署）

```sql
ALTER DATABASE DATAFILE '+DG0/dbfiles/sysaux01' RESIZE 20G;
```

**查询SYSAUX表空间中表的占用情况**

示例

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE,SUM(BYTES)/1024/1024 SIZE_M FROM DBA_SEGMENTS D
WHERE D.TABLESPACE_NAME = 'SYSAUX' GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME    SEGMENT_NAME                    SEGMENT_TYPE           SIZE_M
------------------ ------------------------------- ----------------- -----------
SYSAUX             WRH$_SQLTEXT                    TABLE PARTITION             4
SYSAUX             WRH$_SQLSTAT                    TABLE PARTITION             4
SYSAUX             WRH$_SYSSTAT_PK                 INDEX PARTITION          .625
SYSAUX             WRH$_SYSSTAT                    TABLE PARTITION          .625
SYSAUX             WRH$_SYSTEM_EVENT               TABLE PARTITION          .375
SYSAUX             WRH$_MEM_USED_COMP_PK           INDEX PARTITION          .125
SYSAUX             WRH$_OSSTAT_PK                  INDEX PARTITION          .125
SYSAUX             WRH$_SERVICE_WAIT_CLASS_PK      INDEX PARTITION          .125
SYSAUX             WRM$_SNAPSHOT                   TABLE                    .125
SYSAUX             WRH$_OSSTAT                     TABLE PARTITION          .125
SYSAUX             WRH$_SERVICE_WAIT_CLASS         TABLE PARTITION          .125
SYSAUX             WRH$_MEM_USED_COMP              TABLE PARTITION          .125
SYSAUX             WRM$_DATABASE_INSTANCE          TABLE                    .125
SYSAUX             WRM$_WR_CONTROL                 TABLE                    .125
```
