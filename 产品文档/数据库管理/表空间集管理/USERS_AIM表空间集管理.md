USERS_AIM表空间集是内置的memory mapped的表空间集，memory mapped类的表空间集的文件的所有页面都映射在内存中。

初始的USERS_AIM表空间集大小为[建库参数](../../工具手册/yasboot/建库参数)中的mms_tablespace_set_size，用户可以在安装时根据资源需求配置该参数的值。

**查看USERS_AIM表空间集中的文件信息**

示例（分布式部署）

```sql
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=8
ORDER BY 3,1,2;

DN_NODE   FILENAME                   TS#      BYTES RELATIVE_FNO AUTO_EXTEND  NEXT_SIZE         MAX_SIZE 
--------- ------------------------- ---- ---------- ------------ ----------- ---------- ---------------- 
3-1       TSS_1801_CHUNK_0_FILE_0      8  104857600            0 OFF                  0                0
```

