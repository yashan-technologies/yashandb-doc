In daily use, database administrators can customize tablespaces as needed by adding, deleting, or modifying them. For detailed syntax descriptions regarding tablespace management, please refer to the development manual [CREATE TABLESPACE](../../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLESPACE), [ALTER TABLESPACE](../../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER TABLESPACE), and [DROP TABLESPACE](../../../../Development Guide/SQL Reference Manual/SQL Statements/DROP TABLESPACE).

## Creating Tablespaces

When a database administrator receives a request to create a tablespace, the following aspects need to be considered:

- The business properties served by the tablespace:

  - HEAP tables and TAC tables use a segment page structure and mount a regular data file.

    ```sql
    -- If DATAFILE is not specified, a data file will be created by default.
    CREATE TABLESPACE tablespace_name;
    ```

  - LSC tables use an object structure for stable data and require mounting a data bucket.

    ```sql
    -- The tablespace for LSC must specify DATABUCKET.
    CREATE TABLESPACE tablespace_name DATABUCKET 'lscfile1','lscfile2';
    ```

  - Sensitive or core data can be defined as an encrypted tablespace.

    ```sql
    -- The encryption attribute is determined at the time of tablespace creation and cannot be changed afterward.
    CREATE TABLESPACE tablespace_name DATABUCKET 'lscfile1','lscfile2' ENCRYPTION ENCRYPT;
    ```

  - For tablespaces with extremely high performance requirements, they can be defined as MMS tablespaces, and the MMS_DATA_LOADERS parameter must be configured to determine the preloading strategy.

    ```sql
    CREATE TABLESPACE tablespace_name MEMORY MAPPED;
    ```

- The storage properties of the tablespace:

  - Determine the initial value, extension value, and maximum value for the data file, which is automatically decided by the system by default.

    ```sql
    CREATE TABLESPACE yashan1 DATAFILE 'yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G; 
    ```

  - Determine the extent size, which is automatically decided by the system by default.

    ```sql
    CREATE TABLESPACE yashan1 DATAFILE 'yashan1' SIZE 4M AUTOEXTEND ON NEXT 4M MAXSIZE 1G EXTENT UNIFORM SIZE 64K; 
    ```

  - For MMS tablespaces, determine whether to enable large page memory; enabling large page memory requires the operating system to be configured for large page memory.

    ```sql
    -- Restart required to take effect.
    ALTER SYSTEM SET MMS_USE_LARGE_PAGES=true SCOPE=BOTH;
    ```

## Tablespace Operations

### Offline and Online

When a tablespace is no longer in use or is desired to be inaccessible, it can be taken offline:

```sql
-- By default, all data files in the tablespace must be offline to take the tablespace offline.
ALTER TABLESPACE yashan OFFLINE;

-- The TEMPORARY option allows data files in the offline tablespace to be offline.
ALTER TABLESPACE yashan OFFLINE TEMPORARY;
```

The online operation can restore the offline tablespace:

```sql
ALTER TABLESPACE yashan ONLINE;
```

### Adding and Deleting Data Files

There are specifications for the number of data files and data buckets that a tablespace can mount (YashanDB has a limit of 64). Administrators need to pay attention to this limit to avoid reaching the maximum (when the maximum is reached, it will not be possible to increase the data files, and the number cannot be released by deleting data files). When the limit is reached and there is a need to increase data files or data buckets, creating a new tablespace is a solution.

When adding data buckets, consider the BUCKET_RESERVED_SPACE parameter, which controls the upper limit of disk space occupied by data buckets.

```sql
-- The properties of the newly added data file are determined by the system by default; temporary files require the TEMPFILE keyword.
ALTER TABLESPACE yashan ADD DATAFILE;

ALTER TABLESPACE yashan DROP DATAFILE 'yashan1';

ALTER TABLESPACE lsc_tb ADD DATABUCKET 'lscfile3','lscfile4';

ALTER TABLESPACE lsc_tb DROP DATABUCKET 'lscfile3';
```

### Free Space Reclamation

Shrink the tablespaces in Standalone Deployment and ISC Distributed Cluster Deployment to reclaim free space for use by other tablespaces.

***Example*** for Standalone Deployment and ISC Distributed Cluster Deployment

```sql
-- If KEEP is not specified, the system will maximize the shrinking of the tablespace.
ALTER TABLESPACE system SHRINK SPACE KEEP 100M;
ALTER TABLESPACE system SHRINK SPACE;
```

### Distributed Tablespace Fault Recovery

If a node fails during DDL execution for a tablespace in ISC Distributed Cluster Deployment, YashanDB provides automatic recovery functionality.

```sql
-- To check if the system is operating normally, query the GV$TABLESPACE, GV$DATAFILE, and GV$DATABUCKET views on the CN node to obtain the status of tablespace, datafile, and bucketfile files on the node.
select * from GV$TABLESPACE where name = upper('TABLESPACENAME') order by group_id;
select * from GV$DATAFILE where name like '%DATAFILENAME' order by group_id;
select * from GV$DATABUCKET where url like '%BUCKETFILENAME' order by group_id;
```

The directories for Datafile and Bucketfile are as follows:

- Default directory for Datafile: `${YASDB_DATA}/NODENAME/dbfiles/`
- Default directory for Bucketfile: `${YASDB_DATA}/NODENAME/local_fs/`
- If a directory path is specified, then the file directory shall be based on the actual path: `${YASDB_DATA}/NODENAME/DIRECTORYNAME/`

#### Fault During CREATE TABLESPACE

If a node fails when creating a tablespace, the specific situation and solution are as follows:

- If only the MN node (group_id 1) has TABLESPACE and Datafile or Databucket records, and there are no abnormal TABLESPACE-related DDL records in the GV$PUB_STAT view, it indicates that only the MN node has failed:

  1. The MN node needs to be restarted, and the following statement should be executed manually on the CN node:

     ```sql
     drop tablespace if exists tablespace_name including contents and datafiles;
     ```

  2. Query the GV$TABLESPACE, GV$DATAFILE, and GV$DATABUCKET views to confirm that there are no corresponding TABLESPACE entries.

  3. Check the Datafile and Databucket folders under the MN node to confirm whether there are corresponding TABLESPACE file remnants; if so, manually clean up the relevant folders.

  4. After restarting, execute the CREATE TABLESPACE statement again.

- If both the MN node and some CN/DN nodes have TABLESPACE and Datafile or Databucket records, and there are abnormal TABLESPACE-related DDL push records in the GV$PUB_STAT view of the MN node, it indicates a failure in some CN/DN nodes:

  1. Restart the failed node.
  
  2. After a moment, query the GV$PUB_STAT view in the MN node; if no abnormal TABLESPACE-related DDL records are found, recovery is successful. If there are still abnormal DDL records, you can check the ERR_MSG field to see the reason for failure: if it indicates insufficient DN resources, then resources need to be reallocated appropriately.

- If no nodes have TABLESPACE and Datafile or Databucket records, and the GV$PUB_STAT view of the MN node has no abnormal TABLESPACE-related DDL records, it indicates that only the MN node has failed:
  
  1. Restart the MN node.
  
  2. Execute the CREATE TABLESPACE statement again.

#### Fault During DROP TABLESPACE

If a node fails while deleting a tablespace, the specific situation and solution are as follows:

- If the MN node has TABLESPACE and Datafile or Databucket records, it indicates that only the MN node has failed:

  1. Restart the MN node, and manually execute the following statement on the CN node:

     ```sql
     drop tablespace if exists tablespace_name including contents and datafiles;
     ```

- If the MN node has no TABLESPACE, but CN/DN nodes have TABLESPACE and Datafile or Databucket records, it indicates that some CN/DN nodes have failed:

  1. Restart the failed node to recover. 

#### Fault During ALTER TABLESPACE ADD (DATAFILE | TEMPFILE)

If a node fails while modifying the tablespace data file, the specific situation and solution are as follows:

- If only the MN node (group_id 1) has TABLESPACE and Datafile or Databucket records, and the GV$PUB_STAT view has no abnormal TABLESPACE-related DDL records, it indicates that only the MN node has failed:

  1. The MN node needs to be restarted, and the following statement should be executed manually on the CN node:

     ```sql
     alter tablespace yashan drop datafile datafile_name;
     ```

  2. Query the GV$TABLESPACE, GV$DATAFILE, and GV$DATABUCKET views to confirm that there are no corresponding TABLESPACE records.

  3. Check the Datafile and Databucket folders under the MN node to confirm whether there are any remaining corresponding TABLESPACE files; if there are, manually clean up the corresponding folders.

  4. After restarting, execute the ALTER TABLESPACE ADD (DATAFILE | TEMPFILE) statement again.

- If both the MN node and some CN/DN nodes have TABLESPACE and Datafile or Databucket records, and there are abnormal TABLESPACE-related DDL push records in the GV$PUB_STAT view of the MN node, it indicates a fault in some CN/DN nodes:

  1. Restart the failed node.
  
  2. After a moment, query the GV$PUB_STAT view in the MN node. If no abnormal TABLESPACE-related DDL records are found, recovery is successful; if abnormal DDL records are still present, check the ERR_MSG field to see the reason for failure: if it indicates insufficient DN resources, then resources need to be reallocated appropriately.

- If no nodes have TABLESPACE and Datafile or Databucket records, and the GV$PUB_STAT view of the MN node has no abnormal TABLESPACE-related DDL records, it indicates that only the MN node has failed:
  
  1. Restart the MN node.
  
  2. Execute the ALTER TABLESPACE ADD (DATAFILE | TEMPFILE) statement again.
