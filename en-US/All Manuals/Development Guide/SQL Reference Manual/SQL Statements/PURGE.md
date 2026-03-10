General Description
----

The PURGE statement is used to clear recycle bin data.

In YashanDB, when a table is dropped or truncated, if the recycle bin functionality is enabled ([modify configuration parameter](ALTER SYSTEM) RECYCLEBIN_ENABLED to ON), the data of this table will be moved to the recycle bin for recovery from accidental operations.

PURGE is only applicable to HEAP tables.

Statement Definition
----

**purge::=**

```ebnf+diagram
syntax::= PURGE (TABLE name|INDEX name|TABLESPACE name [USER username]|RECYCLEBIN|DBA_RECYCLEBIN)
```

### 1. TABLE

This statement is used to clear the specified table from the recycle bin. Clearing the table will also clear other objects on the table, such as indexes, LOB, etc.

The name parameter specifies the table name to be purged from the recycle bin, which can be:

- The original table name (the ORIGINAL_NAME column in the [DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) view)

- The BIN$-prefixed system-generated object name (the OBJECT_NAME column in the [DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) view) when the target table entered the recycle bin

### 2. INDEX

This statement is used to clear the specified index from the recycle bin.

The name parameter specifies the index name to be purged from the recycle bin, which can be:

- The original index name (the ORIGINAL_NAME column in the [DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) view)

- The BIN$-prefixed system-generated object name (the OBJECT_NAME column in the [DBA_RECYCLEBIN](../../../参考手册/系统视图/DBA视图/DBA_RECYCLEBIN) view) when the target index entered the recycle bin

### 3. TABLESPACE name [USER username]

This statement is used to clear the specified tablespace from the recycle bin. When a user is specified, it will only clear objects under the specified user in the specified tablespace.

### 4. RECYCLEBIN

This statement is used to clear all objects under the current user in the recycle bin.

### 5. DBA\_RECYCLEBIN

This statement is used to clear all objects in the recycle bin.

***Example*** for Heap tables

```sql
-- Enable recycle bin
ALTER SYSTEM SET RECYCLEBIN_ENABLED=ON;

-- Delete a specific object
DROP TABLE finance_info;
-- Clear the specified object's data from the recycle bin
PURGE TABLE finance_info;

-- Clear recycle bin data of the specified tablespace
PURGE TABLESPACE yashan;
-- Clear all objects data under the current user in the recycle bin
PURGE RECYCLEBIN;
-- Clear all objects data in the recycle bin
PURGE DBA_RECYCLEBIN;
```
