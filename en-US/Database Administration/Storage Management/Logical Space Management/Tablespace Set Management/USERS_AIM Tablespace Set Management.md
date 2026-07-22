The USERS_AIM tablespace set is a built-in memory mapped tablespace set, where all pages of the memory mapped tablespace set files are mapped in memory.

The initial size of the USERS_AIM tablespace set is defined by the parameter mms_tablespace_set_size in the [Database Creation Parameters](../../../../Tools Guide/yasboot/Database Creation Parameters). Users can configure the value of this parameter during installation based on resource requirements.

**View file information in the USERS_AIM tablespace set**

***Example*** for ISC Distributed Cluster Deployment

```sql
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM gv$DATAFILE
WHERE TS#=8
ORDER BY 3,1,2;

DN_NODE   FILENAME                   TS#      BYTES RELATIVE_FNO AUTO_EXTEND  NEXT_SIZE         MAX_SIZE 
--------- ------------------------- ---- ---------- ------------ ----------- ---------- ---------------- 
3-1       TSS_1801_CHUNK_0_FILE_0      8  104857600            0 OFF                  0                0
```
