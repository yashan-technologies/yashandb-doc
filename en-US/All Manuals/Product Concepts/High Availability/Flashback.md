Flashback provides the capability to "reverse" or "undo" database operations, enhancing data security and operational fault tolerance.

- Database Flashback: Allows restoring the entire database to a past point-in-time, such as rolling back corrupted data written across the entire database, restoring data from multiple tables caused by critical accidental operations, or scenarios involving primary-standby business drills.

- Table Flashback: Recovers accidentally dropped objects (e.g., tables) from the Recycle Bin, making deletion operations reversible and rapidly recoverable.

- Data Flashback: Enables querying/restoring historical data of existing tables.

    - Flashback query is commonly used for error tracing, issue diagnosis, and identifying recovery targets.
    
    - Flashback data recovery is commonly used to undo unintended operations and roll back data, and it is a lightweight recovery method.

##  Database Flashback

FLASHBACK DATABASE TO SCN|TIMESTAMP functions similarly to Point-in-Time Recovery (PITR), restoring the database to a recent state as a recovery mechanism.



Database flashback leverages flashback log files for recovery. These logs record pre-modification data at the block level. Executing a flashback effectively rolls back changes by reverting the database to a specified time using incremental log and data changes.



It offers faster recovery than traditional backup/restore methods, reducing reliance on physical backups, and is ideal for human error scenarios or rapid recovery needs.

##  Table Flashback

FLASHBACK ... TO BEFORE restores dropped tables/partitions and their data from the Recycle Bin, effectively reversing operations like DROP, TRUNCATE, and ALTER TABLE TRUNCATE PARTITION.

YashanDB's Recycle Bin operates like an OS recycle bin. When enabled, dropped tables/partitions are renamed and moved to the current tablespace's Recycle Bin (along with dependent objects like indexes and triggers) for potential recovery.

Use FLASHBACK TABLE to restore Recycle Bin objects. However, automated cleanup limits recovery to short-term scenarios. For long-term or complex recovery needs, avoid relying solely on the Recycle Bin.

##  Data Flashback

Data flashback enables querying historical versions of table data:

- Flashback Query: SELECT ... FROM ... AS OF SCN|TIMESTAMP retrieves historical data snapshots.

- Data Restoration: FLASHBACK TABLE ... TO SCN|TIMESTAMP reverses operations like INSERT/DELETE.

YashanDB's UNDO logs record pre-change states of data. During the UNDO retention period (also called "undo retention"), users can query/restore historical data even after updates.

Data flashback enables querying past data without restoring backups. The database uses timestamps/SCNs to locate historical states and applies UNDO logs to reconstruct results.
