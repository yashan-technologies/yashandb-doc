The database recycle bin functions similarly to an operating system's recycle bin, temporarily storing deleted database objects such as tables and indexes. It prevents permanent data loss when objects are deleted, allowing quick and efficient recovery of accidentally deleted objects until the recycle bin is purged.

The recycle bin is attached to the tablespace. After the recycle bin is enabled (see [Recycle Bin Management](./Recycle Bin Management)), when certain objects are deleted without specifying the PURGE keyword, the system will place them in the recycle bin of this tablespace (the object name is modified to a system-generated string starting with BIN).

##  Supported Objects

The recycle bin is only applicable to HEAP tables. Deleting TAC tables, LSC tables, and external tables always results in permanent deletion. The objects supported by the recycle bin and their deletion behaviors are shown in the following table.

|Target Object     | Operation      | Behavior    |
|--------------------|-----------------|---------------------|
| HEAP tables in the SYSTEM tablespace | -          | The target table and its sub-objects never enter the recycle bin (which means they are completely deleted). |
| HEAP tables without full-text indexes in other tablespaces | DROP TABLE (without PURGE)    | The target table, its partitions, indexes, and triggers based on the table are moved to the recycle bin. |
|   | TRUNCATE TABLE                  | The target table, its partitions, indexes, and triggers based on the table are moved to the recycle bin. |
|   | ALTER TABLE TRUNCATE PARTITION  | - If the target partition resides in the SYSTEM tablespace: Does not enter the recycle bin.<br/>- If in other tablespaces: Moves to the recycle bin. |
| HEAP tables with full-text indexes created in other tablespaces | - | The target table and its sub-objects never enter the recycle bin (which means they are completely deleted). |

> **Note**:
>
> - Dropping sub-objects of a HEAP table (e.g., indexes, triggers) via the DROP statement will not move them to the recycle bin.
> 
> - If the tablespace is full or near capacity when performing these operations, objects and data may be purged from the recycle bin immediately.

##  Recycle Bin Object Recovery Rules

When flashback recovering recycle bin objects, the rules are as follows: 

- If the object to be recovered is a table that has been dropped, you need to check whether the original table name is occupied. If it is, you must rename the table during the Flashback operation; otherwise, an error will occur.

- If an object has been dropped multiple times, each Flashback operation will only restore one DROP operation in reverse chronological order. That is, the first Flashback will restore only the most recent DROP. If the previous Flashback operation did not rename the object (retaining the original table name), subsequent Flashback operations will no longer be able to restore the object using the original name.

- If an object has been truncated multiple times, each Flashback operation will only restore one TRUNCATE operation in reverse chronological order. That is, the first Flashback will restore only the most recent TRUNCATE operation.

- If new data is inserted after truncating, executing the table flashback operation will place this new data into the recycle bin.

- Objects always adhere to hierarchical dependencies:

    - Partitions that entered the recycle bin via TRUNCATE TABLE cannot be restored individually. Subpartitions that entered via TRUNCATE PARTITION of a primary partition cannot be restored individually.

    - Sub-objects (indexes, triggers, etc.) cannot be restored separately. They are automatically recovered with their parent table/partition if still present in the recycle bin.