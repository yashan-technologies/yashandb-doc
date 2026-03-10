The USERS tablespace set is the default user tablespace set.

Indexes/AC based on sharded tables cannot specify tablespaces and tablespace sets, and indexes/AC will default to being stored in the same tablespace set as the table.

**Modify the maximum expandable space of the USERS tablespace set**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users MAXSIZE 22T;
```

**Modify the size for each automatic extension of Data Files within the USERS tablespace set**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users NEXT 100M;
```

**Modify the size of Data Files within the USERS tablespace set**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users resize 300M;
```

**Shrink the size of Data Files within the USERS tablespace set**

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users SHRINK space;
```

**View file information in the USERS tablespace set**

The system-built tablespace sets will generate records starting with 'TSS_' in GV$TABLESPACE.

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT group_id,group_node_id,id 
FROM GV$TABLESPACE ts
WHERE ts.name LIKE 'TSS_%' AND ts.memory_mapped=false;

    GROUP_ID GROUP_NODE_ID           ID 
------------ ------------- ------------ 
           3             1            7
           4             1            7
           5             1            7

SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=7 AND group_id=3 AND group_node_id=1
ORDER BY 3,1,2;

DN_NODE   FILENAME                  TS#      BYTES RELATIVE_FNO AUTO_EXTEND   NEXT_SIZE       MAX_SIZE 
--------- ------------------------- --- ---------- ------------ ----------- ----------- -------------- 
3-1       TSS_1800_CHUNK_0_FILE_0     7   68157440            0 ON             67108864   549755813888
3-1       TSS_1800_CHUNK_0_FILE_1     7    1048576            1 ON             67108864   549755813888
```
