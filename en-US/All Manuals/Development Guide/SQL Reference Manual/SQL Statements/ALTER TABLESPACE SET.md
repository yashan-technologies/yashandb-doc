General Description
----

The ALTER TABLESPACE SET statement is used to change the relevant properties of an existing tablespace set in ISC Distributed Cluster Deployment.

A tablespace set is a logical storage unit in YashanDB ISC Distributed Cluster Deployment used for storing sharded table and related data information, physically corresponding to the data files on each DN node.

Currently, only non-memory-mapped tablespace sets can be modified.

In ISC Distributed Cluster Deployment, when this statement is executed, the system will check for any outstanding DDL statements that have not been recovered. If any exist, an error will be raised, and execution of this statement must wait until the corresponding DDL has been successfully recovered.

> **Note**: 
>
> All occurrences of CHUNK_NUM in this document refer to the total number of Chunks in the current ISC Distributed Cluster Deployment environment. This value can be calculated using the database creation parameter USERS_DATASPACE_SCALE_OUT_FACTOR * the number of DN groups at the time of database creation. The database creation parameter USERS_DATASPACE_SCALE_OUT_FACTOR is configured during installation and cannot be modified afterwards. You can consult the database administrator to obtain the value of this parameter.

Statement Definition
----

**alter tablespace set::=**

```ebnf+diagram
syntax::= ALTER TABLESPACE SET tablespace_set_name (MAXSIZE size_clause | NEXT size_clause | RESIZE size_clause | shrink_clause | databucket_clause)
```

**[shrink_clause](#shrinkclause)::=**

```ebnf+diagram
syntax::= SHRINK SPACE [KEEP size_clause]
```

**[databucket_clause](#databucketclause)::=**

```ebnf+diagram
syntax::= add_databucket_clause | alter_databucket_clause | drop_databucket_clause
```

**[add_databucket_clause](#adddatabucketclause)::=**

```ebnf+diagram
syntax::= ADD DATABUCKET ((bucket_clause) {"," (bucket_clause)})
```

**[bucket_clause](#bucketclause)::=**

```ebnf+diagram
syntax::= "'bucket_name'" [s3_bucket_clause] [MAXSIZE size_clause]
```

**[s3_bucket_clause](#s3bucketclause)::=**

```ebnf+diagram
syntax::= S3 "(" URL "'url'" ["," REGION "'region'"] "," ACCESS KEY "'access_key'" "," SECRET KEY "'secret_key'" ")"
```

**[alter_databucket_clause](#alterdatabucketclause)::=**

```ebnf+diagram
syntax::= ALTER DATABUCKET "'bucket_name'" (READONLY | READWRITE)
```

**[drop_databucket_clause](#dropdatabucketclause)::=**

```ebnf+diagram
syntax::= DROP DATABUCKET "'bucket_name'"
```

### 1. tablespace\_set\_name

The name of the tablespace set.

### 2. MAXSIZE size\_clause

This statement modifies the maximum scalable space of the tablespace set.

The range of size_clause is [128 * DB_BLOCK_SIZE * CHUNK_NUM, 4G * DB_BLOCK_SIZE * CHUNK_NUM - 1]. When the DB_BLOCK_SIZE parameter is set to the default value of 8K, this range is [1M * CHUNK_NUM, 32T * CHUNK_NUM - 1].

Increasing the maximum scalable space of the tablespace set will correspondingly increase the maximum scalable space of the data files on all DN groups:

1. The sum of the maximum scalable space of all data files constitutes the maximum scalable space of the tablespace set.
2. When increasing the maximum scalable space of the tablespace set, the system will equally divide the new maximum space among each DN group. Each DN group first extends the maximum of the existing files to the maximum, then continues to create new files.
3. The initial size of the newly created data file is 1M by default, and the maximum scalable space of other files, except for the last file, is the maximum.

Reducing the maximum scalable space of the tablespace set will correspondingly reduce the maximum scalable space of the data files on all DN groups.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- View the current maximum max_size of the USERS tablespace set (TSS_2616 is the system default users tablespace identifier)
SELECT SUM(max_size) FROM GV$DATAFILE WHERE name LIKE '%/TSS_2616%_CHUNK_%' AND GROUP_ID > 2 AND GROUP_NODE_ID = 1;

SUM(MAX_SIZE)
-------------
   2.3090E+13

-- Expand the maxsize of the USERS tablespace set to 22T
ALTER TABLESPACE SET users MAXSIZE 22T;
```

### 3. NEXT size\_clause

This statement modifies the size by which each data file in the tablespace set automatically expands.

The range of size_clause is [512 * DB_BLOCK_SIZE, 32768 * DB_BLOCK_SIZE]. When the DB_BLOCK_SIZE parameter is set to the default value of 8K, this range is [4M, 256M].

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Continuing from the previous example
ALTER TABLESPACE SET users NEXT 100M;

-- Query the modified data file information
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=7
ORDER BY 3,1,2;
DN_NODE   FILENAME                     TS#      BYTES RELATIVE_FNO AUTO_EXTEND   NEXT_SIZE       MAX_SIZE 
--------- -------------------------- ----- ---------- ------------ ----------- ----------- -------------- 
3-1       TSS_1800_CHUNK_0_FILE_0        7   68157440            0 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_1        7    1048576            1 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_2        7    1048576            2 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_3        7    1048576            3 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_0        7    1048576            0 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_1        7    1048576            1 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_2        7    1048576            2 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_3        7    1048576            3 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_0        7    1048576            0 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_1        7    1048576            1 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_2        7    1048576            2 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_3        7    1048576            3 ON            104857600   549755813888
																				
```

### 4. RESIZE size\_clause

This statement modifies the size of the data file in the tablespace set.

The range of size_clause is [128 * DB_BLOCK_SIZE * CHUNK_NUM, MAXSIZE], where MAXSIZE refers to the current maximum scalable space of the tablespace set. When the DB_BLOCK_SIZE parameter is set to the default value of 8K, the minimum value is 1M * CHUNK_NUM.

If the size specified by the user is less than the current actual size, no modification will occur.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- Continuing from the previous example
ALTER TABLESPACE SET users RESIZE 300M;

-- Query the modified data file information
SELECT group_id||'_'||group_node_id dn_node,
SPLIT(name,'/',-1) filename,
TS#,BYTES,RELATIVE_FNO,AUTO_EXTEND,NEXT_SIZE,MAX_SIZE 
FROM GV$DATAFILE
WHERE TS#=7
ORDER BY 3,1,2;
DN_NODE   FILENAME                     TS#      BYTES RELATIVE_FNO AUTO_EXTEND   NEXT_SIZE       MAX_SIZE 
--------- -------------------------- ----- ---------- ------------ ----------- ----------- -------------- 
3-1       TSS_1800_CHUNK_0_FILE_0        7  104857600            0 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_1        7    1048576            1 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_2        7    1048576            2 ON            104857600   549755813888
3-1       TSS_1800_CHUNK_0_FILE_3        7    1048576            3 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_0        7  104857600            0 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_1        7    1048576            1 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_2        7    1048576            2 ON            104857600   549755813888
4-1       TSS_1800_CHUNK_1_FILE_3        7    1048576            3 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_0        7  104857600            0 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_1        7    1048576            1 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_2        7    1048576            2 ON            104857600   549755813888
5-1       TSS_1800_CHUNK_2_FILE_3        7    1048576            3 ON            104857600   549755813888
																				
```

<span id="shrinkclause" name="shrinkclause" class="yaslink"></span>

### 5. shrink\_clause

This statement is used to reclaim unused space based on the space usage of each data file, thereby reducing the size of the tablespace set.

#### 5.1. keep size\_clause

Shrink the tablespace set to a specified size. If the specified retained value exceeds the size of the tablespace set, the shrink operation will fail and return an error.

If KEEP is not specified, the system will maximize the shrinkage of the tablespace set.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET users SHRINK SPACE KEEP 100M;
 
ALTER TABLESPACE SET users SHRINK SPACE;
```

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

### 6. databucket\_clause

This statement is used to modify the databucket (data bucket) information in the tablespace set.

<span id="adddatabucketclause" name="adddatabucketclause" class="yaslink"></span>

#### 6.1. add\_databucket\_clause

This statement is used to add a databucket, with multiple entries allowed, separated by commas. The description and constraints for the newly created databuckets are the same as those in the [databucket_clause](CREATE TABLESPACE SET.html#databucketclause) subclause in CREATE TABLESPACE SET.

<span id="bucketclause" name="bucketclause" class="yaslink"></span>

##### 6.1.1. bucket\_clause

Described the same as the [bucket_clause](CREATE TABLESPACE SET.html#bucketclause) subclause in CREATE TABLESPACE SET.

<span id="s3bucketclause" name="s3bucketclause" class="yaslink"></span>

###### 6.1.1.1. s3\_bucket\_clause

Described the same as the [s3_bucket_clause](CREATE TABLESPACE SET.html#s3bucketclause) subclause in CREATE TABLESPACE SET.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET tbs_tb ADD DATABUCKET '?/local_fs/lscfile3' MAXSIZE 1G;
```

<span id="alterdatabucketclause" name="alterdatabucketclause" class="yaslink"></span>

#### 6.2. alter\_databucket\_clause

This statement is used to modify the read and write attributes of the databucket. The created databuckets are all read-write by default.

##### 6.2.1. bucket\_name

Described the same as the bucket_name in the [bucket_clause](CREATE TABLESPACE SET.html#bucketclause) subclause in CREATE TABLESPACE SET.

##### 6.2.2. readonly|readwrite

readonly indicates read-only, and readwrite indicates read-write.

Note that after the databucket is modified to read-only, no write operations of any form are supported. However, DML statements executed by users (excluding Bulkload operations) are not affected. During Bulkload operations, if there are no writable databuckets under the tablespace, an error will occur due to the need to generate SCOL data.

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET tbs_tb ALTER DATABUCKET '?/local_fs/lscfile3' READONLY;
```

<span id="dropdatabucketclause" name="dropdatabucketclause" class="yaslink"></span>

#### 6.3. drop\_databucket\_clause

This statement is used to delete a databucket, allowing the specification of only one databucket for deletion at a time.

##### 6.3.1. bucket\_name

Described the same as in [alter_databucket_clause](#alterdatabucketclause). 

***Example*** for ISC Distributed Cluster Deployment

```sql
ALTER TABLESPACE SET tbs_tb DROP DATABUCKET '?/local_fs/lscfile3';
```
