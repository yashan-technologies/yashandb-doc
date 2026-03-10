General Description
----

CREATE TABLESPACE is used to create custom user tablespaces, which support transparent encryption.

In addition to custom tablespace creation on demand, YashanDB has a built-in set of basic tablespaces. For related information, please refer to the [tablespace_clause](CREATE DATABASE.html#tablespaceclause) of CREATE DATABASE or [Tablespace Management](../../../Database Administration/Tablespace Management/00Tablespace Management.html#build-in).

> **Note**:
>
> When executing this statement in ISC Distributed Cluster Deployment, the system first checks for any abnormal un-recovered DDL statements. If any exist, an error will be reported, and execution of this statement must wait until the corresponding DDL has been successfully recovered.
>
> If a node failure occurs during the execution of this statement in ISC Distributed Cluster Deployment, please refer to [User Tablespace Management](../../../Database Administration/Tablespace Management/General Tablespace Management).

In ISC Distributed Cluster Deployment, the number of tablespaces must satisfy the condition `Number of Tablespace Sets * (CHUNK_NUM + 1) + Number of Tablespaces <= 2048`.

Statement Definition
----

**create tablespace::=**

```ebnf+diagram
syntax::= CREATE (permanent_tablespace_clause | temporary_tablespace_clause | swap_tablespace_clause)
```

**[permanent_tablespace_clause](#permanenttablespaceclause)::=**

```ebnf+diagram
syntax::= [ BIGFILE | SMALLFILE ] TABLESPACE tablespace_name [datafile_clause] [extent_management_clause] [MEMORY MAPPED] [databucket_clause] [encryption_clause] [compress_clause]
```

**[temporary_tablespace_clause](#temporarytablespaceclause)::=**

```ebnf+diagram
syntax::= (TEMPORARY TABLESPACE | LOCAL TEMPORARY TABLESPACE FOR (ALL | LEAF)) tablespace_name [datafile_clause] [extent_management_clause]
```

**[swap_tablespace_clause](#swaptablespaceclause)::=**

```ebnf+diagram
syntax::= (SWAP TABLESPACE | LOCAL SWAP TABLESPACE) tablespace_name [datafile_clause]
```

**[datafile_clause](#datafileclause)::=**

```ebnf+diagram
syntax::= ((DATAFILE | TEMPFILE) (file_specification) {"," (file_specification)})
```

**[file\_specification](#filespecification)::=**

```ebnf+diagram
syntax::= "file_name" SIZE size_clause [AUTOEXTEND (OFF|(ON [NEXT size_clause] [MAXSIZE (UNLIMITED|size_clause)]))] [PARALLEL parallel]
```

**[extent\_management\_clause](#extentmanagementclause)::=**

```ebnf+diagram
syntax::= EXTENT (AUTOALLOCATE|UNIFORM SIZE size_clause)
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

**[encryption\_clause](#encryptionclause)::=**

```ebnf+diagram
syntax::= ENCRYPTION [USING ("'" AES128 "'" | "'" SM4 "'")] ENCRYPT
```

**[compress\_clause](#compressclause)::=**

```ebnf+diagram
syntax::= COMPRESS [LZ4|ZSTD]
```

<span id="permanenttablespaceclause" name="permanenttablespaceclause" class="yaslink"></span>

### 1. permanent\_tablespace\_clause

This statement is used to create a new tablespace in the database, where objects are stored in data files or data buckets.

#### 1.1. BIGFILE | SMALLFILE

Only used for syntax compatibility, with no actual meaning.

<span id="temporarytablespaceclause" name="temporarytablespaceclause" class="yaslink"></span>

### 2. temporary\_tablespace\_clause

This statement is used to specify the creation of a temporary tablespace, where the data lasts only during the user session, and the objects in the temporary tablespace are stored in temporary files. 

In ISC Distributed Cluster Deployment, only the built-in temporary tablespace (named TEMP) can be used, and manual creation of temporary tablespaces is not allowed.



#### 2.1. Temporary Tablespace

The creation syntax of the temporary tablespace is CREATE TEMPORARY TABLESPACE, and the created temporary files are shared and used by all nodes.

- Storage paths for temporary files:

  - In Standalone Deployment, the temporary file path must be a local path. If only a relative path is specified, it is stored in the $YASDB_DATA/dbfiles directory by default.
  
  - In YAC/Distributed Cluster Deployment, the temporary file path must be a YFS path.

- The final number of created files completely depends on what is specified in the SQL statement.

- In Standalone Primary-Standby Deployment or Primary-Standby Cluster Deployment, when the primary database/cluster creates a local temporary tablespace, the standby database/cluster will synchronously create the corresponding local temporary tablespace and a shadow-state temporary file with the same name.
  
  - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

  - After the primary-standby switch, the existing local temporary tablespaces in the new primary database/cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local temporary tablespace or add new files to the existing local temporary tablespace for use.

  - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary database/cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary database/cluster. After other standby database/clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



#### 2.2. Local Temporary Tablespace

The creation syntax is CREATE LOCAL TEMPORARY TABLESPACE FOR ALL|LEAF, where FOR ALL|LEAF is only for compatibility and has no practical meaning.

The local temporary tablespace is only applicable to YAC/Distributed Cluster Deployment. When created, independent temporary files will be created for each instance within the cluster, and these files are not shared among instances.

- The temporary file path supports local disk paths and YFS paths, but mixing is not allowed within the same tablespace. If only a relative path is specified when creating a local temporary tablespace, it is stored locally by default, that is, in the $YASDB_DATA/dbfiles directory.

- The final number of created files is the number specified in the SQL statement multiplied by the number of instances.

- In Primary-Standby Cluster Deployment, when the primary cluster creates a local temporary tablespace, the standby cluster will synchronously create the corresponding local temporary tablespace and a shadow-state temporary file with the same name.
  
  - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

  - After the primary-standby switch, the existing local temporary tablespaces in the new primary cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local temporary tablespace or add new files to the existing local temporary tablespace for use.

  - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary cluster. After other standby clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



***Example*** for Standalone Deployment

```sql
CREATE TEMPORARY TABLESPACE shared_temp TEMPFILE '?/dbfiles/shared_temp.dbf' SIZE 4M;
```

***Example*** for YAC Deployment

```sql
CREATE TEMPORARY TABLESPACE shared_temp TEMPFILE '+DG0/dbfiles/shared_temp.dbf' SIZE 4M;
CREATE LOCAL TEMPORARY TABLESPACE FOR ALL local_temp TEMPFILE '?/dbfiles/local_temp.dbf' SIZE 4M;
```

<span id="swaptablespaceclause" name="swaptablespaceclause" class="yaslink"></span>

### 3. swap\_tablespace\_clause

This statement is used to specify the creation of a SWAP tablespace, where the data lasts only during the user session. Objects in the SWAP tablespace are stored in temporary files. 

In ISC Distributed Cluster Deployment, only the built-in SWAP tablespace (named SWAP) can be used, and manual creation of SWAP tablespaces is not allowed.



#### 3.1. SWAP Tablespace

The creation syntax of the SWAP tablespace is CREATE SWAP TABLESPACE, and the created temporary files are shared and used by all nodes.

- Storage paths for temporary files:

  - In Standalone Deployment, the temporary file path must be a local path. If only a relative path is specified, it is stored in the $YASDB_DATA/dbfiles directory by default.
  
  - In YAC/Distributed Cluster Deployment, the temporary file path must be a YFS path.

- The final number of created files completely depends on what is specified in the SQL statement.

- In Standalone Primary-Standby Deployment or Primary-Standby Cluster Deployment, when the primary database/cluster creates a local SWAP tablespace, the standby database/cluster will synchronously create the corresponding local SWAP tablespace and a shadow-state temporary file with the same name.
  
  - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

  - After the primary-standby switch, the existing local SWAP tablespaces in the new primary database/cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local SWAP tablespace or add new files to the existing local SWAP tablespace for use.

  - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary database/cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary database/cluster. After other standby database/clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



#### 3.2. Local SWAP Tablespace

The creation syntax is CREATE LOCAL SWAP TABLESPACE.

The local SWAP tablespace is only applicable to YAC/Distributed Cluster Deployment. When created, independent temporary files will be created for each instance within the cluster, and these files are not shared among instances.

- The temporary file path supports local disk paths and YFS paths, but mixing is not allowed within the same tablespace. If only a relative path is specified when creating a local SWAP tablespace, it is stored locally by default, that is, in the $YASDB_DATA/dbfiles directory.

- The final number of created files is the number specified in the SQL statement multiplied by the number of instances.

- In Primary-Standby Cluster Deployment, when the primary cluster creates a local SWAP tablespace, the standby cluster will synchronously create the corresponding local SWAP tablespace and a shadow-state temporary file with the same name.
  
  - The shadow file is only recorded in the ctrlfile and no physical file will be generated on the disk. You can query the SHADOW field of V$DATAFILE to obtain its relevant information.

  - After the primary-standby switch, the existing local SWAP tablespaces in the new primary cluster cannot be used because all their existing files are in the shadow state. It is recommended to create a new local SWAP tablespace or add new files to the existing local SWAP tablespace for use.

  - If you need to delete a shadow file, you must ensure that the creator of the target file is the current primary cluster (if not, you need to switch first) and execute the DROP TEMPFILE statement on this primary cluster. After other standby clusters receive the redo log of DROP TEMPFILE, they will clean up the shadow records.



***Example*** for Standalone Deployment

```sql
CREATE SWAP TABLESPACE shared_swap TEMPFILE '?/dbfiles/shared_swap' SIZE 4M;  
```

***Example*** for YAC Deployment

```sql
CREATE SWAP TABLESPACE shared_swap TEMPFILE '+DG0/dbfiles/shared_swap' SIZE 4M;
CREATE LOCAL SWAP TABLESPACE local_swap TEMPFILE '?/dbfiles/local_swap' SIZE 4M;
```

### 4. tablespace\_name

This statement is used to specify the name of the tablespace to be created. This cannot be omitted and must comply with YashanDB's [object naming conventions](../Basic SQL Elements/Identifiers).

In ISC Distributed Cluster Deployment, be aware that the name of the tablespace being created must not match the names of existing tablespaces, or an error will be returned.

***Example***

```sql
-- Create a tablespace using all default options
CREATE TABLESPACE yashan;
```

<span id="datafileclause" name="datafileclause" class="yaslink"></span>

### 5. datafile\_clause

This statement is used to specify the data file corresponding to the tablespace being created. It can be omitted, in which case the system will automatically create a data file according to the following rules:

- The file name is generated by combining the tablespace name and the sequence number of the data file within the tablespace, e.g. tablespace_name1, tablespace_name2..., and is converted to uppercase.
- The default size of the file is 8192 blocks, and the file path is the system's default data file path.
- For non-MEMORY MAPPED tablespaces, the default file has automatic extension enabled, with the next size being 8192 blocks and the maximum size being 64MB blocks.
- If the extent allocation method is not explicitly specified, the default allocation method for extents is automatic allocation by the system.
- The TEMPFILE keyword can only be used to create temporary tablespaces or SWAP tablespaces.
- For local temporary tablespaces and local SWAP tablespaces, adding a file is equivalent to adding a group of files, with the number of files in each group being equal to the number of cluster instances.

<span id="filespecification" name="filespecification" class="yaslink"></span>

#### 5.1. file\_specification

Specifies multiple data files separated by `,`. 

It can be omitted, in which case the system will create a data file under the default data file path named after the tablespace (converted to uppercase), with a size of 8192 blocks, auto-extend enabled, extending by 8192 blocks, with a maximum of 64MB blocks. 

The parallelism for creating the data file can be specified through PARALLEL, with a range of 1 to 8. If parallelism is not specified, the database will automatically adjust it based on file size: parallelism will be 1 for files not exceeding 1G, 8 for files exceeding 128G, and 4 for files between 1G and 128G.

##### 5.1.1. file\_name

Specifies the name of the data file.

- In Standalone Deployment, you can use filename without path (e.g., `yashan`), absolute paths (e.g., `/data/yashan/yasdb_data/db-1-1/dbfiles/yashan`), or relative paths replacing `$YASDB_DATA` with `?` or `.` (e.g., `?/dbfiles/yashan` or `./dbfiles/yashan`). When using filename without path or relative paths, the actual file creation will take the system's default data file path `$YASDB_DATA/dbfiles`.

- In YAC Deployment, different types of file storage paths have different requirements:

  - DATAFILE: Must be a [YFS](../../../共享集群/集群文件系统/00集群文件系统) path. You can use filename without path (e.g., `yashan`) or the full disk group path (e.g., `+DG0/dbfiles/yashan`). When using filename without path, the actual file creation will take the default disk group path `+DG0/dbfiles`.

  - TEMPFILE: Local temporary tablespaces and local SWAP tablespace temporary files support YFS paths or local disk paths. YFS paths follow the same rules as DATAFILE, and local disk paths follow the same rules as Standalone Deployment.

- In ISC Distributed Cluster Deployment, you can use filename without path (e.g., `yashan`) or relative paths replacing `$YASDB_DATA` with `?` or `.` (e.g., `?/dbfiles/yashan` or `./dbfiles/yashan`). The actual file creation will take the system's default data file path `$YASDB_DATA/dbfiles`.

##### 5.1.2. size

Specifies the size of the data file, adhering to the general [size_clause](../General SQL Syntax/size_clause) definition.

A single data file must have at least 128 blocks to store metadata.

A single data file within an UNDO tablespace can have a maximum size of 8MB blocks, while a single data file outside of an UNDO tablespace can have a maximum size of 64MB blocks.

The size of a data file equals the database block size multiplied by the number of blocks in the data file. For example, when the database block size is 8K, the minimum value for a single data file outside of the UNDO tablespace is 1M, and the maximum value is 512G.

##### 5.1.3. autoextend on|autoextend off

This enables or disables automatic extension for the created data file. The default value is off if this statement is omitted. When automatic extension is enabled:

*   NEXT size_clause: This specifies the size by which the data file automatically extends each time; the default is 8192 blocks.
*   MAXSIZE UNLIMITED/size_clause: This determines the maximum extent to which the data file can extend; the default is 64MB blocks. UNLIMITED means no maximum limit.

AUTOEXTEND ON cannot be specified for MMS tablespaces.

##### 5.1.4. parallel

When creating a large data file, the degree of parallelism can be specified by this statement to improve the speed of large file creation. If this statement is not specified, the system automatically selects parallelism based on file size. For example, parallelism will be 1 for files not exceeding 1G, 8 for files exceeding 128G, and 4 for file sizes between 1G and 128G.

The value of parallel should be between 1 and 8.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
-- Regular path
CREATE TABLESPACE yashan1 DATAFILE 'yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 2;
```

***Example*** for YAC Deployment

```sql
-- YFS path
CREATE TABLESPACE yashan1 DATAFILE '+DG0/yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 2;
```

<span id="extentmanagementclause" name="extentmanagementclause" class="yaslink"></span>

### 6. extent\_management\_clause

This statement is used to specify the allocation method for extents when objects in the tablespace request space. AUTOALLOCATE means the system automatically allocates extent space based on the current size of the object, while UNIFORM means that a fixed amount of extent space is allocated each time for the object.

*   The extent allocation method cannot be modified after the tablespace is created.
*   If a temporary tablespace is created without specifying an extent allocation method, the default is UNIFORM with a size of 8 blocks.
*   The extent allocation method cannot be specified for SWAP tablespaces; the default is UNIFORM with a size of 8 blocks.
*   If a non-temporary tablespace is created without specifying an extent allocation method, the default is AUTOALLOCATE.
*   Each data file in a tablespace using the UNIFORM allocation method must be larger than the UNIFORM SIZE.

***Example***

```sql
CREATE TABLESPACE yashan2 DATAFILE 'yashan2' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G EXTENT UNIFORM SIZE 64K; 
CREATE TABLESPACE yashan3 DATAFILE 'yashan3' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G PARALLEL 6 EXTENT UNIFORM SIZE 64K;  
```

### 7. memory mapped

When the MEMORY MAPPED keyword is specified, it indicates that all pages of the files created for the tablespace will be mapped into memory (AIM: all in memory). YashanDB refers to this type of tablespace as a memory mapped space (MMS). MMS cannot be created in YAC Deployment.

MMS tablespaces support pre-loading of all data into memory at database startup, enhancing access performance. The specific pre-loading strategy can be configured through the MMS_DATA_LOADERS parameter.

You can check whether a tablespace is an MMS tablespace by querying the MEMORY_MAPPED field in V$TABLESPACE.

In ISC Distributed Cluster Deployment, if the MEMORY MAPPED keyword is specified, the [databucket_clause](#databucketclause) cannot be specified simultaneously.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
CREATE TABLESPACE yashan4 DATAFILE 'yashan4' SIZE 4M EXTENT UNIFORM SIZE 64K MEMORY MAPPED;  
```

<span id="databucketclause" name="databucketclause" class="yaslink"></span>

### 8. databucket\_clause

Specifies the path information for the object storage directory. Multiple paths can be specified separated by `,`, where each path represents the creation of a bucket (data bucket). It can be omitted; if omitted, the default created tablespace will not mount any bucket.

You can view all the information of the buckets created in the current system by querying the V$DATABUCKET view.

YashanDB allows a maximum of 64 data buckets to be mounted under a single tablespace. The total maximum number of data buckets that can be mounted in the database depends on the database creation parameters. Please refer to the maxdatabuckets statement description in the [CREATE DATABASE](CREATE DATABASE) section.

LSC tables must have their data bucket mounted in the corresponding tablespace before they can be created.

In YAC Deployment, specifying the databucket_clause is not allowed.

<span id="bucketclause" name="bucketclause" class="yaslink"></span>

#### 8.1. bucket\_clause

This statement is used to specify bucket information, such as path, size, etc.

YashanDB supports creating local storage buckets and S3 (Simple Storage Service) buckets for table space sets. Local storage buckets store data in local paths, while S3 buckets are designed for cloud storage over the internet.

> **Note**: 
>
> The S3 bucket functionality is not enabled by default. If you wish to use it, please contact our technical support for assistance.

##### 8.1.1. bucket\_name

For different bucket types, bucket_name has different meanings:

- For local storage buckets, bucket_name can be specified as a logical name or an absolute or relative path in the local file system. However, in the context of the database, it is treated as a path. If only the bucket name is specified, a directory created with that bucket name will default to the $YASDB_DATA/local_fs directory. If a bucket path is specified, the system will perform the following validity checks on the specified path:

  - The specified directory can only be under the $YASDB_DATA/local_fs directory.

  - The specified directory must not be the same as other buckets, nor can it be a parent or child directory of them.

  - The parent directory of the specified directory must exist, and the system must have read and write privileges on the specified directory.

  - Absolute paths are not allowed in ISC Distributed Cluster Deployment.

- For S3 buckets, bucket_name is a logical name and must consist of letters, digits, or underscores. It is used to query specific bucket information within the database.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
-- The following statement will create a new lsc_tb tablespace and simultaneously create two buckets, lscfile1 and lscfile2, under the $YASDB_DATA/local_fs directory. It will also default to creating a data file named LSC_TB0.
CREATE TABLESPACE lsc_tb DATABUCKET '?/local_fs/lscfile1','?/local_fs/lscfile2';
```

<span id="s3bucketclause" name="s3bucketclause" class="yaslink"></span>

##### 8.1.2. s3\_bucket\_clause

This statement is used to specify the access information for the S3 bucket.

###### 8.1.2.1. url

Used to specify the access address; it cannot be omitted and has a maximum length of 1023 bytes.

###### 8.1.2.2. region

Used to specify the physical region to which the S3 bucket belongs. It is optional; if omitted, the default settings of the object storage service will be used, with a maximum length of 127 bytes.

###### 8.1.2.3. access key

Used to identify the identity of the client; it cannot be omitted and has a maximum length of 127 bytes.

###### 8.1.2.4. secret key

Access key; it cannot be omitted and has a maximum length of 127 bytes.

##### 8.1.3. MAXSIZE size\_clause

Used to specify the size of the bucket, with a minimum value of 1048576 (1M) and a maximum value of 9223372036854775807. It can be omitted; if omitted, the default value is UNLIMITED.

***Example*** for Standalone Deployment

```sql
-- The following statement will create a s3_tablespace and mount an S3 bucket
CREATE TABLESPACE s3_tablespace DATABUCKET 's3_bucket3' S3(URL '192.168.0.1:8000/s3bucket',ACCESS key 'ak',secret key 'sk') MAXSIZE 8G;
```

<span id="encryptionclause" name="encryptionclause" class="yaslink"></span>

### 9. encryption\_clause

This statement is used to specify the encryption properties of the tablespace, beginning with ENCRYPTION. This statement can be omitted, indicating that the created tablespace does not have encryption properties.

The rules for using encrypted tablespaces are as follows:

- In Standalone Deployment and YAC Deployment, before creating an encrypted tablespace, key management-related configurations must be completed, including creating wallets, enabling wallets, setting master keys, etc. For specific operations, refer to [Configuring Wallet](../../../Product Security/Encryption/Storage Encryption/Key Management.html#configuringwallet).
- Built-in tablespaces and temporary tablespaces cannot be specified as encrypted tablespaces.
- For table objects within encrypted tablespaces, indexes and ACs created on them must also reside in an encrypted tablespace.
- The encryption property specified during the creation of the tablespace cannot be changed later.

For partitioned tables, the system does not restrict whether the encryption properties of each partition's containing tablespace are consistent, thus allowing only part of the table's data to be encrypted. During updates that trigger partition movements, the data will be stored in plaintext or ciphertext according to the encryption property of the new partition.

**ENCRYPT**

Enables the encryption functionality for the tablespace. When specified as ENCRYPT, it indicates that the created tablespace is an encrypted tablespace, meaning that the data stored on the medium corresponding to this tablespace will be encrypted.

**USING encryption_algorithm**

This statement is used to specify the encryption algorithm, supporting AES128 and SM4. It can be omitted, and if omitted, the default algorithm is SM4.

> **Note**:
>
> If you need to use the national secret algorithm SM4 or TDE-related key management functionality, please first consult the [Preparation of Dependencies](../../../Installation and Upgrade/Installation and Deployment/Pre-Installation Preparation/Preparing Dependencies) to ensure that the required tools are installed on the server system.

***Example***

```sql
-- The following statement will create a tablespace and specify it as an encrypted tablespace.
CREATE TABLESPACE encrypt_tb ENCRYPTION ENCRYPT;
-- The following statement will create an encrypted tablespace and specify it to use the AES128 encryption algorithm.
CREATE TABLESPACE aes128_tb ENCRYPTION USING 'AES128' ENCRYPT;
```

<span id="compressclause" name="compressclause" class="yaslink"></span>

### 10. compress\_clause

This statement is used to specify the compression properties of the tablespace, beginning with COMPRESS. The compression algorithms only support LZ4 and ZSTD; if not specified, the default compression algorithm is LZ4. This statement can be omitted, indicating that the created tablespace is a non-compressed tablespace.

When specified as COMPRESS, it indicates that the created tablespace is a compressed tablespace, meaning that the data stored on the medium corresponding to this tablespace will be compressed. YashanDB has the following constraints for compressed tablespaces:

- Built-in tablespaces cannot be specified as compressed tablespaces.
- The compression property specified during the creation of the tablespace cannot be changed later.
- If the file system where the data file of the tablespace resides does not support punch hole or its page size is not 1024, 2048, or 4096, a compressed tablespace cannot be created.
- Some file systems do not support compressed tablespaces; common supported file systems include: XFS, ext4, Btrfs, tmpfs(5), gfs2(5), etc.

In YAC Deployment, creating local temporary tablespaces or local swap tablespaces with local disk files allows the specification of compress_clause; other tablespace creation operations do not allow the specification of compress_clause.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
-- The following statement will create a tablespace and specify it as a compressed tablespace.
CREATE TABLESPACE cmprss_tb COMPRESS;
CREATE TABLESPACE lz4_tb COMPRESS lz4;
CREATE TABLESPACE zstd_tb COMPRESS zstd;
```
