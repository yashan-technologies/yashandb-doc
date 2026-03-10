USERS表空间是默认的用户表空间，用于存储永久用户对象和私有信息。

在创建一个用户而没有指定其使用的表空间时，该用户下除分布表外的所有信息都会放入到USERS表空间中，分布表相关信息存储在[表空间集](../表空间集管理/00表空间集管理)中。

**为USERS表空间增加数据文件**

示例（单机、存算一体分布式集群部署）

```sql
ALTER TABLESPACE users ADD DATAFILE '?/dbfiles/users02' SIZE 100M;
```

示例（共享集群部署）

```sql
ALTER TABLESPACE users ADD DATAFILE '+DG0/dbfiles/users02' SIZE 100M;
```

**为USERS表空间调整数据文件大小**


此功能不适用于存算一体分布式部署。


示例（单机部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/users02' RESIZE 50M;
```

示例（共享集群部署）

```sql
ALTER DATABASE DATAFILE '+DG0/dbfiles/users02' RESIZE 20G;
```

**为USERS表空间删除数据文件**

示例（单机部署）

```sql
ALTER TABLESPACE users DROP DATAFILE '?/dbfiles/users02';
```

示例（共享集群部署）

```sql
ALTER TABLESPACE users DROP DATAFILE '+DG0/dbfiles/users02';
```

> **Note**: 
>
> 在上述语句中指定数据文件路径时，单机部署中可按绝对或相对路径指定，存算一体分布式集群部署中只允许按相对路径指定，共享集群部署中按绝对路径且只能是YFS路径指定。

**查看使用USERS表空间的对象信息**

示例

```sql
SELECT D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE, SUM(BYTES)/1024/1024 SIZE_M FROM DBA_SEGMENTS D 
WHERE D.TABLESPACE_NAME = 'USERS'
GROUP BY D.TABLESPACE_NAME, D.SEGMENT_NAME, D.SEGMENT_TYPE ORDER BY SIZE_M DESC;

TABLESPACE_NAME            SEGMENT_NAME         SEGMENT_TYPE           SIZE_M
-------------------------- -------------------- ----------------- -----------
USERS                      SYS_C_21             INDEX                    .125
USERS                      AREA                 TABLE                    .125
```
