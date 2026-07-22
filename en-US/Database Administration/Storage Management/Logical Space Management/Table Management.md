## Storage Space Planning

When building a database, administrators need to design the storage space and allocate it to business users. Database developers use the default storage space to create table objects, allowing developers to focus less on underlying storage constraints, thus simplifying application logic.

When creating table objects using [CREATE TABLE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLE) without specifying a TABLESPACE, the default is the user's associated tablespace. Table data will enter the planned storage space, while temporary table data will enter the system's temporary tablespace.

In addition, it is recommended that administrators plan the following storage spaces for developers to specify:

- Establish a corresponding index tablespace for each user tablespace.

- If necessary, establish a separate tablespace for the partitions of partitioned tables.

- Establish one or more independent tablespaces for LOBs.

It is important to note that for LSC tables, administrators need to pre-plan and create a databucket tablespace for storing SCOL stable data.

> **Note**: 
>
> TAC tables and LSC tables mutable data are organized by column, with each column stored in a segment-page structure.
>
> Even if a single piece of data is inserted, each column will pre-allocate a specific number (up to 384) of pages.
>
> Especially in scenarios with many table partitions and columns, inserting data may lead to insufficient tablespace errors. Reference error code [YAS-02007](../../../Reference Manual/Error Codes) for handling methods.

## Space Optimization

Database administrators can manage table storage space through SQL statements. For detailed syntax descriptions, please refer to the development manual [CREATE TABLE](../../../Development Guide/SQL Reference Manual/SQL Statements/CREATE TABLE) and [ALTER TABLE](../../../Development Guide/SQL Reference Manual/SQL Statements/ALTER TABLE).

**HEAP Tables**

Due to row-stored characteristics, frequent updates and deletions on tables may lead to fragmentation, causing the table's occupied space to become bloated. In this case, the shrink table functionality can reorganize the storage structure of HEAP tables and shrink the data space.

***Example*** for Heap tables

```sql
ALTER TABLE orders_info ENABLE ROW MOVEMENT;
-- After shrink, releases the vacated extent for other tables to use
ALTER TABLE orders_info SHRINK SPACE;
-- COMPACT means do not release
ALTER TABLE orders_info SHRINK SPACE COMPACT;

-- Shrink a specific partition space
ALTER TABLE orders_info MODIFY PARTITION p_orders_info_1 SHRINK SPACE COMPACT;
```

> **Note**: 
>
> The shrink table operation can be performed online and does not affect DML execution.

**LSC Table Stable Data**

LSC table stable data is stored in slice files. Space optimization for this data includes: 1) merging discrete small slice files; 2) sorting unordered slice files.

YashanDB automatically starts a background data conversion task to perform the above optimizations on slice files. During the optimization period, there will be additional resource consumption. Users can disable this operation for specific tables based on actual situations:

***Example*** for Standalone Deployment LSC tables

```sql
-- After disabling, the system will no longer automatically optimize the space of stable data for the orders_info table
ALTER TABLE orders_info DISABLE COMPACT;
```

And when necessary, manually initiate a space optimization for the table to improve query performance:

***Example*** for Standalone Deployment LSC tables

```sql
ALTER TABLE orders_info ENABLE COMPACT;
ALTER TABLE orders_info ALTER SLICE ALL COMPACT;
```

> **Note**: 
>
> - If resources permit, it is recommended not to turn off the COMPACT switch, allowing the system to automatically determine the timing for space optimization and execute it.
>
> - If manual execution of COMPACT is necessary, it is recommended to arrange it during business idle times to avoid affecting other operations.