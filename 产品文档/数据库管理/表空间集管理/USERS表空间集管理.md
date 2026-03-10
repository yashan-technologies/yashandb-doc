USERS表空间集是默认的用户表空间集。

基于分布表的索引/AC是不能指定表空间和表空间集，且索引/AC会默认存储到和表相同的表空间集。

**修改USERS表空间集的最大拓展空间**

示例（分布式部署）

```sql
ALTER TABLESPACE SET users MAXSIZE 22T;
```

**修改USERS表空间集内部的数据文件每次自动扩展的大小**

示例（分布式部署）

```sql
ALTER TABLESPACE SET users NEXT 100M;
```

**修改USERS表空间集内部的数据文件的大小**

示例（分布式部署）

```sql
ALTER TABLESPACE SET users resize 300M;
```

**收缩USERS表空间集内部的数据文件的大小**

示例（分布式部署）

```sql
ALTER TABLESPACE SET users SHRINK space;
```

**查看USERS表空间集中的文件信息**

系统内置的表空间集将在GV$TABLESPACE中生成以'TSS_'开头的name记录。

示例（分布式部署）

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
