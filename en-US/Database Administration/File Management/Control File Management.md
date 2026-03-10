The default control files have 3 copies (up to a maximum of 8). The specific number and file paths for the control files can be specified in the configuration parameter file.

>**Note**:
>
>This article uses Standalone Deployment as an example. The $YASDB_DATA path varies across different deployment forms. Please refer to the actual returned results and modify to the actual control file paths as needed.

## Viewing Control Files

Method 1: View using SQL statement:

```sql
show parameter CONTROL_FILES;

NAME              VALUE                                                            
----------------- ---------------------------------------------------------------- 
CONTROL_FILES     ('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1', '/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2', '/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3')
```

Method 2: View through dynamic views when the database is in mount or open state:

```sql
SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM V$CONTROLFILE;
  ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
---- ------------------------------------------------- ------------ -------------- -----------
   0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
   1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
   2 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3            8192           3507    28729344
```

## Modifying Control File Path

Changing the database's control file path to a different location can enhance the security of the control files.

> **Warn**:
>
> To move the control files to another path, you must first manually change the control file path, and then move the corresponding control files to the target location.
>
> Do not move control files to another location while in normal operation in a production environment.

1. Modify the control file parameter:

  ```sql
  ALTER SYSTEM SET control_files=('/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1','/data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2','/data/yashan/yasdb_data/db-1-1/backup/ctrl3') scope=spfile;
  ```
2. Shut down the database:

  ```sql
  SHUTDOWN IMMEDIATE;
  ```
3. Move the ctrl3 control file:

  ```shell
  $ mv /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl3 /data/yashan/yasdb_data/db-1-1/backup/ctrl3
  ```
4. Check and confirm the ctrl3 control file at the file system level:

  ```shell
  ls -l /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1
  ls -l /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2
  ls -l /data/yashan/yasdb_data/db-1-1/backup/ctrl3
  ```

5. Start the database:

  ```shell
  $ yasboot cluster start -c yashandb
  ```
  If the database starts successfully with no errors, the control file path modification is successful.

6. After the database starts, log into the database using *yasql* to check and confirm the ctrl3 control file (execute when the database is in mount or open state):

  ```sql
  SELECT ID,NAME,BLOCK_SIZE,FILE_SIZE_BLKS,BYTES FROM V$CONTROLFILE;
    ID NAME                                                BLOCK_SIZE FILE_SIZE_BLKS       BYTES
  ---- ------------------------------------------------- ------------ -------------- -----------
    0 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl1            8192           3507    28729344
    1 /data/yashan/yasdb_data/db-1-1/dbfiles/ctrl2            8192           3507    28729344
    2 /data/yashan/yasdb_data/db-1-1/backup/ctrl3             8192           3507    28729344
  ```
