The database Recycle Bin functions similarly to an operating system's recycle bin, temporarily storing deleted database objects such as tables and indexes. It prevents permanent data loss when objects are deleted, allowing quick and efficient recovery of accidentally deleted objects until the Recycle Bin is purged.

The recycle bin is only applicable to HEAP tables. Deleting TAC tables, LSC tables, and external tables will always result in permanent deletion.

The recycle bin depends on the tablespace. When the recycle bin is enabled, deleting a HEAP table in a tablespace without specifying the PURGE keyword causes the system to place the table object and its data into the recycle bin of that tablespace (the table name is renamed to a BIN$-prefixed system-generated string).

##  Supported Objects

When the Recycle Bin is [enabled](./Recycle Bin Management), the following objects can be moved to the Recycle Bin: HEAP tables, their dependent objects, and associated data. 

The behavior of deletion operations is described in the table below. 

|Target Object     | Operation      | Behavior    |
|--------------------|-----------------|---------------------|
| HEAP tables and their dependent objects in the SYSTEM tablespace | -          | Never enter the Recycle Bin.               |  
| HEAP tables in other tablespaces    | DROP TABLE (without PURGE)    | The table, its partitions, indexes, and triggers based on the table are moved to the Recycle Bin. |  
|   | TRUNCATE TABLE                  | The table, its partitions, indexes, and triggers based on the table are moved to the Recycle Bin. |
|   | ALTER TABLE TRUNCATE PARTITION  | - If the target partition resides in the SYSTEM tablespace: Does not enter the Recycle Bin.<br/>- If in other tablespaces: Moves to the Recycle Bin. |  

> **Note**:
>
> - Dropping dependent objects of a HEAP table (e.g., indexes, triggers) via the DROP statement will not move them to the Recycle Bin.
> 
> - If the tablespace is full or near capacity when performing these operations, objects and data may be purged from the Recycle Bin immediately.

##  Recycle Bin Object Recovery Rules

When flashback recovering recycle bin objects, the rules are as follows: 

- If the object to be recovered is a table that has been dropped, you need to check whether the original table name is occupied. If it is, you must rename the table during the Flashback operation; otherwise, an error will occur.

- If an object has been dropped multiple times, each Flashback operation will only restore one DROP operation in reverse chronological order. That is, the first Flashback will restore only the most recent DROP. If the previous Flashback operation did not rename the object (retaining the original table name), subsequent Flashback operations will no longer be able to restore the object using the original name.

- If an object has been truncated multiple times, each Flashback operation will only restore one TRUNCATE operation in reverse chronological order. That is, the first Flashback will restore only the most recent TRUNCATE operation.

- If new data is inserted after truncating, executing the table flashback operation will place this new data into the recycle bin.

- Objects always adhere to hierarchical dependencies:

    - Partitions that entered the Recycle Bin via TRUNCATE TABLE cannot be restored individually. Subpartitions that entered via TRUNCATE PARTITION of a primary partition cannot be restored individually.

    - Dependent objects (indexes, triggers, etc.) cannot be restored separately. They are automatically recovered with their parent table/partition if still present in the Recycle Bin.