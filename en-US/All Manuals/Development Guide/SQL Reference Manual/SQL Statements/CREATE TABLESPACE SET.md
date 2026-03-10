## General Description

The CREATE TABLESPACE SET statement is used to create a new tablespace set in ISC Distributed Cluster Deployment mode.

A tablespace set is a logical storage unit in YashanDB ISC Distributed Cluster Deployment, used to store sharded tables and related data information, physically corresponding to the data files on each DN node.

> **Note**: 
>
> All CHUNK_NUM mentioned in this document refers to the total number of Chunks in the current ISC Distributed Cluster Deployment environment. This value can be calculated as USERS_DATASPACE_SCALE_OUT_FACTOR * the number of DN groups when creating the database. The parameter USERS_DATASPACE_SCALE_OUT_FACTOR is configured during installation and cannot be modified later. Please consult the database administrator for the value of this parameter.

In ISC Distributed Cluster Deployment, when executing this statement, the system will check for any abnormal unrecovered DDL statements. If any exist, an error will be reported and the statement cannot be executed until the corresponding DDL is successfully recovered.

In ISC Distributed Cluster Deployment, the number of tablespace sets must satisfy the condition `Number of tablespace sets * (CHUNK_NUM + 1) + Number of tablespaces <= 2048`.

## Statement Definition

**create tablespace set::=**

```ebnf+diagram
syntax::= CREATE TABLESPACE SET tablespace_set_name (([databucket_clause] ON dataspace_name (MAXSIZE size_clause [NEXT size_clause] [SIZE size_clause]))|(ON dataspace_name MEMORY MAPPED SIZE size_clause ))
```

**[databucket_clause](#databucketclause)::=**

```ebnf+diagram
syntax::= DATABUCKET ((bucket_clause) {"," (bucket_clause)})
```

**[bucket_clause](#bucketclause)::=**

```ebnf+diagram
syntax::= "bucket_name" [s3_bucket_clause] [MAXSIZE size_clause]
```

**[s3_bucket_clause](#s3bucketclause)::=**

```ebnf+diagram
syntax::= S3 "(" URL "'url'" ["," REGION "region"] "," ACCESS KEY "access_key" "," SECRET KEY "secret_key" ")"
```

### 1. tablespace\_set\_name

This statement is used to specify the name of the tablespace set to be created. It is mandatory and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

### 2. databucket\_clause

Specifies the path information for object storage directories. Multiple paths can be separated by `,`. Each path represents the creation of a bucket (data bucket). This parameter is optional; if omitted, it will be mounted to the system's default generated bucket, and if specified, no default bucket will be generated.

You can query the GV$DATABUCKET view to see information about all buckets created in the current system.

In YashanDB, the maximum number of DataBuckets allowed under a single tablespace set is 64. The maximum number of DataBuckets allowed in the database depends on the database creation parameters; please refer to the description of the maxdatabuckets statement in the [CREATE DATABASE](CREATE DATABASE) section.

Once this statement is specified, the [MEMORY MAPPED](#memorymapped) keyword cannot be specified simultaneously.

<span id="bucketclause" name="bucketclause" class="yaslink"></span>

#### 2.1. bucket\_clause

This statement is used to specify bucket information, such as path and size.

YashanDB supports creating local storage buckets and S3 (Simple Storage Service) buckets. Local storage buckets store data in a local path, while S3 buckets are internet-facing cloud storage.

> **Note**: 
>
> The S3 bucket functionality is not enabled by default. Please contact our technical support for assistance if needed.

##### 2.1.1. bucket\_name

For different bucket types, bucket_name has different meanings:

- For locally stored buckets, bucket_name can be specified as a logical name or as an absolute or relative path in the local filesystem. However, in the database context, they are equivalent to paths. If only the bucket name is specified, a directory with that bucket name will be created by default in the $YASDB_DATA/local_fs directory. If a bucket path is specified, the system will perform validity checks on the specified path:

  - The specified directory must be within the $YASDB_DATA/local_fs directory.

  - The specified directory cannot be the same as other buckets and cannot be its parent or child directory.

  - The parent directory of the specified directory must exist, and the system must have read and write privileges on the specified directory.

  - In ISC Distributed Cluster Deployment, absolute paths are not allowed.

- For S3 buckets, bucket_name is a logical name used to query specific bucket information within the database.

The newly created bucket name must not conflict with system built-in buckets; otherwise, an error will be returned. You can view the GV$DATABUCKET view to get information about all the buckets currently in the system.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- The following statement will create a new tablespace set tbs_tb and:
-- Create two custom data buckets, lsc_bucket1 and lsc_bucket2, in the dn's $YASDB_DATA/local_fs and dbfiles directories, respectively, and a default data file with the name TSS_OID_CHUNK_CHUNKID_FILE_ID;
-- A default root data bucket named TSS_OID_ROOT_DATABUCKET and a root data file named TSS_OID_ROOT_DATAFILE will be created in the mn/cn's $YASDB_DATA/local_fs and dbfiles directories.
CREATE TABLESPACE SET tbs_tb DATABUCKET '?/local_fs/lsc_bucket1','?/local_fs/lsc_bucket2' ON USERS MAXSIZE 8G;
```

<span id="s3bucketclause" name="s3bucketclause" class="yaslink"></span>

##### 2.1.2. s3\_bucket\_clause

This statement is used to specify access information for the S3 bucket.

###### 2.1.2.1. url

Used to specify the access address, which is mandatory and has a maximum length of 1023 bytes.

###### 2.1.2.2. region

Used to specify the physical region of the S3 bucket, which is optional. If omitted, the default configuration of the object storage service will be used, with a maximum length of 127 bytes.

###### 2.1.2.3. access key

Used to identify the client identity, which is mandatory and has a maximum length of 127 bytes.

###### 2.1.2.4. secret key

Access key, which is mandatory and has a maximum length of 127 bytes.

##### 2.1.3. MAXSIZE size\_clause

Used to specify the size of the bucket, with a minimum value of 1048576 (1M) and a maximum value of 9223372036854775807. This parameter is optional; if omitted, it defaults to UNLIMITED.

***Example*** for ISC Distributed Cluster Deployment

```sql
-- The following statement will create a tablespace named s3_tablespace and mount an S3 bucket.
CREATE TABLESPACE SET s3_tablespace DATABUCKET 's3_bucket3' S3(URL '192.168.0.1:8000/s3bucket',ACCESS key 'ak',secret key 'sk') ON USERS MAXSIZE 8G;
```

### 3. dataspace\_name

This statement is used to specify the data space to which the tablespace set belongs. It is mandatory and currently only supports the users data space.

<span id="memorymapped" name="memorymapped" class="yaslink"></span>

### 4. MEMORY MAPPED

When the MEMORY MAPPED keyword is specified, it indicates that all pages of the created tablespace set's files will be mapped in memory. YashanDB names this type of tablespace set as a memory-mapped tablespace set.

Memory-mapped tablespace sets support preloading of all data in the tablespace set into memory during database startup, improving access performance.

Properties of a memory-mapped tablespace set cannot be modified after its creation.

### 5. SIZE size\_clause

This statement is used to specify the preallocated size of the memory-mapped tablespace set and is mandatory.

The value range for size_clause is [128 * DB_BLOCK_SIZE * CHUNK_NUM, 4GB * DB_BLOCK_SIZE * CHUNK_NUM - 1].

***Example*** for ISC Distributed Cluster Deployment

```sql
CREATE TABLESPACE SET mm_tss
ON USERS
MEMORY MAPPED SIZE 8G;
```

### 6. MAXSIZE size\_clause

This statement is used to specify the maximum extendable space of the tablespace set. This parameter is mandatory for non-memory mapped tablespace sets and cannot be less than the preallocated size. This parameter is not used for memory-mapped tablespace sets.

The value range for size_clause is [128 * DB_BLOCK_SIZE * CHUNK_NUM, 4G * DB_BLOCK_SIZE * CHUNK_NUM - 1]. When the DB_BLOCK_SIZE parameter is the default value of 8K, this range is [1M * CHUNK_NUM, 32T * CHUNK_NUM - 1].

### 7. NEXT size\_clause

This statement is used to specify the automatic growth size of each data file contained in the tablespace set.

The default value for size_clause is 8192 * DB_BLOCK_SIZE. When the user sets it, the value range is [512 * DB_BLOCK_SIZE, 32768 * DB_BLOCK_SIZE]. When the DB_BLOCK_SIZE parameter is the default value of 8K, this range is [4M, 256M].

### 8. SIZE size\_clause

This statement is used to specify the preallocated size of the tablespace set. It is optional; if omitted, it will be preallocated according to the sizes of the data files and buckets.

The value range for size_clause is [128 * DB_BLOCK_SIZE * CHUNK_NUM, MAXSIZE], where MAXSIZE indicates the maximum extendable space of the current tablespace set. When the DB_BLOCK_SIZE parameter is the default value of 8K, the minimum value is 1M * CHUNK_NUM.

***Example*** for ISC Distributed Cluster Deployment

```sql
CREATE TABLESPACE SET tss1
ON USERS
MAXSIZE 8G SIZE 7G;
```
