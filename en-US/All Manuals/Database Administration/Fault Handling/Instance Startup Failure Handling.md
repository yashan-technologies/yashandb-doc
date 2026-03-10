## Environment Variables Not Set or Incorrect

The following are three key environment variables for YashanDB instances to start successfully; incorrect settings will prevent startup to the NOMOUNT and subsequent stages. Adjust the environment variables to the correct values based on the actual situation:

- YASDB_HOME: Default software directory for YashanDB
- YASDB_DATA: Default data directory for YashanDB
- LD_LIBRARY_PATH: Directory for YashanDB dependency packages; this environment variable must be set correctly before using yasboot sql or yaql.

## Control File Validation Failure



YashanDB performs control file verification when starting the instance. The following situations will prevent the instance from starting to the MOUNT and subsequent stages:

- **Control file actual path does not match the CONTROL_FILES parameter configured path**

  When starting the database instance, if an error like `failed to open file …… , errno 2, error message "No such file or directory"` occurs, it indicates that the actual path of the control file does not match the path configured in the CONTROL_FILES parameter. The solution is as follows:

  - Method 1: Modify the control file path at the OS level, or generate the corresponding new files at the path indicated in the error message using the existing files.

  - Method 2: Change the CONTROL_FILES parameter after starting the instance to the NOMOUNT stage and then restart the instance.

- **Database version in the control file is inconsistent with the instance version**

  When starting the database instance, if an error like `database version x.x.x incompatible with YashanDB version x.x.x` occurs, it indicates that the database version in the control files is inconsistent with the instance version. Choose whether to perform a version upgrade based on the actual situation.

- **Database ID of control file group members is inconsistent**

  When starting the database instance, if an error like `database id of the control file does not match other control files` occurs, it indicates that the database ID of control file group members is inconsistent. This situation usually occurs when multiple database instances exist on a single server, and the CONTROL_FILES parameter is configured with control files of different databases. Modify it to the correct control file path.

- **The entire set of control files does not match the database**

  When starting the database instance, if an error like `invalid file: ……, the reason: mismatching with the control file` occurs, it indicates that the entire set of control files does not match the database. The solutions are as follows:

  - Method 1: Find the correct control files at the OS level and move them to correct paths.

  - Method 2: Change the CONTROL_FILES parameter after starting the instance to the NOMOUNT stage and then restart the instance.

  - Method 3: Restore the latest available backup set through [recovery](../备份与恢复/00备份与恢复).


- **All control files missing or corrupted**

  If the physical files of all control files are missing or corrupted, it can only be resolved by restoring the latest available backup set through [recovery](../备份与恢复/00备份与恢复).



## Data File Missing or Corrupted

- **Built-in tablespace data file missing or corrupted**

  If the built-in tablespace data file is missing or corrupted, the instance will not be able to start to the MOUNT and subsequent stages.

  In this case, the latest available backup set can usually be restored through [recovery](../备份与恢复/00备份与恢复); if no backup set exists, recovery can be achieved through file recovery or physical recovery, etc.

- **User data file missing or corrupted**

  When the data file mounted for the user tablespace is missing or corrupted, it can usually be resolved by restoring the latest available backup set through [recovery](../备份与恢复/00备份与恢复). If no backup set exists, emergency handling can be done using the emergency channel provided by YashanDB to ensure the database can start, open, and run normally. For specific operations, please refer to [Emergency Handling After Data File Corruption](./数据文件损坏应急处理).

## Redo File Missing or Corrupted

If the redo file is missing or corrupted, the instance will not be able to start to the MOUNT and subsequent stages.

In this case, the latest available backup set can usually be restored through [recovery](../备份与恢复/00备份与恢复).

##  Database instance startup failure during cluster startup

When multiple servers issue instance startup commands almost simultaneously, multiple database instances may experience concurrent startups, potentially leading to internal conflicts that cause instance startup failures.

**Possible Observation Points**: The db run log contains: `[AXC] trigger shutdown instance due to database is not open`.

**Mitigation Solutions**: Avoid issuing instance startup commands from multiple servers simultaneously; when manually starting instances, log into each server in sequence and operate accordingly, ensuring that instances are started to an open state.
