For tables that cannot be quickly restored via [Flashback](../Flashback/00Flashback), you can use YashanDB's physical [Backup and Recovery](../Backup and Recovery/00Backup and Recovery) functionality and the [Import](../../Tools Guide/imp/00imp) and [Export](../../Tools Guide/exp/00exp) functionality for emergency recovery.

The operation process is as follows:

1. Backup all data files of the existing database to prevent errors in the remaining steps of the process.

2. Restore the database backup to the standby database environment. At a minimum, the following should be restored:

    - SYSTEM and SYSAUX tablespaces

    - Tablespaces containing the restored or retroactive segments

    - Independent tablespaces containing the data to be retrieved

3. Perform an incomplete recovery on the backup using the restored backup control file, until before the deletion of the table.

4. Export the lost data from the temporary restored version of the database.

5. Import the lost tables into the production database.

6. Recycle the standby database environment.