SYSTEM表空间是创建数据库时包含在数据库中的一个必需的管理表空间，也是建库时创建的第一个表空间，YashanDB使用SYSTEM表空间来管理数据库。

SYSTEM表空间包含以下信息：

-   数据字典

-   包含数据库管理信息的表和视图

-   已编译的存储对象，如触发器、过程和包

SYSTEM表空间像任何其他表空间一样被管理，但需要更高级别的特权，并且在某些方面受到限制。例如，不能重命名或删除SYSTEM表空间，也不能将SYSTEM表空间脱机。

**为SYSTEM表空间添加数据文件**

> **Note**：
>
> 指定数据文件路径时，单机部署中可按绝对或相对路径指定，共享集群部署中按绝对路径指定且只能为YFS路径，分布式部署中只允许按相对路径指定。

示例（单机、分布式部署）

```sql
ALTER TABLESPACE SYSTEM ADD DATAFILE '?/dbfiles/system01' SIZE 10G AUTOEXTEND OFF;
```

示例（共享集群部署）

```sql
ALTER TABLESPACE SYSTEM ADD DATAFILE '+DG0/system01' SIZE 10G AUTOEXTEND OFF;
```

**为SYSTEM表空间删除数据文件**

示例（单机部署）

```sql
ALTER TABLESPACE SYSTEM DROP DATAFILE '?/dbfiles/system01';
```

示例（共享集群部署）

```sql
ALTER TABLESPACE SYSTEM DROP DATAFILE '+DG0/system01';
```

**为SYSTEM表空间调整数据文件大小**

此功能仅适用于单机部署。

示例（单机部署）

```sql
ALTER DATABASE DATAFILE '?/dbfiles/system01' RESIZE 20G;
```

**查看SYSTEM表空间中空间占用情况**

示例

```sql
SELECT * FROM 
(SELECT TABLESPACE_NAME,SEGMENT_NAME,SEGMENT_TYPE,SUM(BYTES)/1024/1024 SIZE 
FROM DBA_SEGMENTS 
WHERE TABLESPACE_NAME='SYSTEM'
GROUP BY TABLESPACE_NAME,SEGMENT_NAME,SEGMENT_TYPE) 
ORDER BY SIZE DESC;

TABLESPACE_NAME      SEGMENT_NAME               SEGMENT_TYPE             SIZE
-------------------- -------------------------- ----------------- -----------
SYSTEM               WRH$_SQLSTAT_PK            INDEX PARTITION             1
SYSTEM               WRH$_SQLTEXT_PK            INDEX PARTITION         .5625
SYSTEM               WRH$_SQLTEXT_INDEX         INDEX PARTITION         .3125
SYSTEM               WRH$_SQLSTAT_INDEX         INDEX PARTITION         .3125
SYSTEM               COL$                       TABLE                     .25
SYSTEM               I_COL2                     INDEX                   .1875
SYSTEM               VIEW$                      TABLE                   .1875
SYSTEM               I_OBJ3                     INDEX                    .125
SYSTEM               WRH$_SYSTEM_EVENT_PK       INDEX                    .125
SYSTEM               I_COL1                     INDEX                    .125
SYSTEM               I_DEPENDENCY2              INDEX                    .125
SYSTEM               I_DEPENDENCY1              INDEX                    .125
SYSTEM               DEPENDENCY$                TABLE                    .125
SYSTEM               OBJ$                       TABLE                    .125
SYSTEM               CDEF$                      TABLE                   .0625
SYSTEM               SEQ$                       TABLE                   .0625
SYSTEM               PARTCOL$                   TABLE                   .0625
SYSTEM               SYN$                       TABLE                   .0625
SYSTEM               ARGUMENT$                  TABLE                   .0625
SYSTEM               USERAUTH$                  TABLE                   .0625
SYSTEM               PACKAGE_ITEMS$             TABLE                   .0625
SYSTEM               I_IND1                     INDEX                   .0625
SYSTEM               I_ICOL1                    INDEX                   .0625
SYSTEM               I_USER2                    INDEX                   .0625
SYSTEM               I_CDEF1                    INDEX                   .0625
SYSTEM               I_CDEF3                    INDEX                   .0625
SYSTEM               I_CDEF5                    INDEX                   .0625
SYSTEM               I_SEQ1                     INDEX                   .0625
SYSTEM               I_PARTCOL                  INDEX                   .0625
SYSTEM               I_TABPART_OBJ              INDEX                   .0625
SYSTEM               I_INDPART_OBJ              INDEX                   .0625
SYSTEM               I_SYN1                     INDEX                   .0625
SYSTEM               I_PROC1                    INDEX                   .0625
SYSTEM               I_ARG2                     INDEX                   .0625
SYSTEM               I_OBJ_ROLE_PRIVS           INDEX                   .0625
```

