The SYSTEM tablespace is a required management tablespace included in the database when it is created. It is also the first tablespace created during database creation. YashanDB uses the SYSTEM tablespace to manage the database.

The SYSTEM tablespace contains the following information:

-   Data dictionary

-   Tables and views containing database management information

-   Compiled storage objects, such as triggers, procedures, and packages

The SYSTEM tablespace is managed like any other tablespace, but it requires a higher level of privilege and is subject to certain restrictions. For example, it cannot be renamed or deleted, nor can the SYSTEM tablespace be taken offline.

**Adding Data File to the SYSTEM Tablespace**

> **Note**: 
>
> When specifying the data file path, for Standalone Deployment, both absolute and relative paths can be specified. For YAC Deployment, only absolute paths can be specified, and they must be YFS paths. For ISC Distributed Cluster Deployment, only relative paths are allowed.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SYSTEM ADD DATAFILE '?/dbfiles/system01' SIZE 10G AUTOEXTEND OFF;
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE SYSTEM ADD DATAFILE '+DG0/dbfiles/system01' SIZE 10G AUTOEXTEND OFF;
```

**Deleting Data File from the SYSTEM Tablespace**

***Example*** for Standalone Deployment

```sql
ALTER TABLESPACE SYSTEM DROP DATAFILE '?/dbfiles/system01';
```

***Example*** for YAC Deployment

```sql
ALTER TABLESPACE SYSTEM DROP DATAFILE '+DG0/dbfiles/system01';
```

**Resizing Data File in the SYSTEM Tablespace**

This functionality is only applicable to Standalone Deployment.

***Example*** for Standalone Deployment

```sql
ALTER DATABASE DATAFILE '?/dbfiles/system01' RESIZE 20G;
```

**Viewing Space Usage in the SYSTEM Tablespace**

***Example***

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
