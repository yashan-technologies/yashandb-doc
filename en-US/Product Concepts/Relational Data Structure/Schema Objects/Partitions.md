## Overview of Partition

YashanDB can split large-scale data into smaller, more manageable objects, known as partitions. By managing data through partitions, ineffective data access can be reduced, improving access and operational performance under large-scale data conditions. Tables can be partitioned based on certain conditions, with different partitions managed independently. Partitioned tables offer a more efficient and convenient way to handle data operations.

Each partition is an independent object that has its own segments and segment properties. Even if one partition is corrupted, it does not affect other partitions.

For example, if a sales manager needs to retrieve sales volume for a specific month, the sales table can be partitioned by month, with sales data for each month being a separate partition. This approach allows for faster data retrieval compared to indexing the month column in a non-partitioned table. Moreover, if the sales data for a particular month is corrupted, it will not affect the operations on sales data from other months.

In YashanDB, data exists in three storage forms: tables, indexes, and LOBs (Large Objects). YashanDB provides respective partitioning capabilities for each form.

- Partition Tables: It is generally recommended to manage tables larger than 2GB using partitioning. Table partitions are not directly associated with other object partitions, with each table having multiple segments managing corresponding partitioned data.

- Partition Indexes: There are two types of partition indexes: local index and global index.

    - Local Index: The index follows the table partition, meaning the index partition corresponds one-to-one with the table partition.

    - Global Index: The index segments may point to any table partition or all table partitions.

- Partition LOB: LOB data is an extension storage of table data; in some sense, LOB data should be partitioned alongside table data. Users can decide on the location of LOB partitions, but the type and number of LOB partitions are still managed uniformly by the partition table.

### Partition Key

The partition key is a set of distinctive data made up of one or more columns, used to determine where the corresponding complete data is stored in a specific data partition. It is a characteristic description of the data partition. Although the partition key itself has no storage significance, every partition has a partition key. Partition pruning and partition locating are centered around the partition key.

The SQL engine generates a partition key from the current query data and calculates the partitions where the data may reside.

### Partition Boundaries

Partition boundaries are a storage structure that holds the boundary values for partitions, but only range partitions, list partitions, and interval partitions have boundary values.

### Partition Strategies

Different partitioning methods arise from various management strategies based on partition keys:

- Range Partition: Supports multi-column partitioning, dividing partitions based on combined values of the partition key. The partition key for range partitions is monotonically increasing, and the partition key characteristics are determined by the current partition and the previous partition's boundaries.

- Hash Partition: Supports multi-column partitioning, calculating a hash value based on the combined values of the partition key to determine which partition the data falls into. Hash partitions do not have specific boundary characteristics and are influenced by the hash algorithm and the number of partitions.

- List Partition: Supports multi-column partitioning, with each partition determined by a set of distinct hash characteristic values of the partition keys. List partitions support a default partition; if the partition key for a row does not match any partition other than the default partition, that row is stored in the default partition.

- Interval Partition: Supports single-column partitioning and limited data types. Interval partitions extend range partitions; users specify the range partition during creation, and the last range partition is called the transition partition, with new partitions created automatically based on interval values.

A single partition strategy only supports a single data distribution method. YashanDB also supports composite partitioning, where data is first distributed to partitions based on one specified partition strategy (e.g., Range Partition) and then further distributed to sub-partitions based on a second specified partition strategy (e.g., List Partition).

#### Range Partition

In range partitioning, YashanDB disperses data into different partitions based on the partition key values. Range partitioning is the most common type of partition, often used with dates.

- The partition boundary for each partition is defined by the current partition's high value. Data with partition key values belonging to the range [the previous partition's high value, the current partition's high value] belongs to the current partition.

- Supports multi-column partition keys.

- Supports specifying MAX VALUE.

#### Hash Partition

In hash partitioning, YashanDB computes the hash value corresponding to the partition key based on the user's specified hash algorithm and maps the rows to the respective partitions based on these hash values (i.e., the internal hash function applied to the rows determines the target partition for each row). The linear hashing algorithm can ensure a roughly equal number of rows in each partition.

- Disperses data into different partitions based on the partition key's hash value.

- The number of partitions can be specified during table creation.

#### List Partition

In list partitioning, YashanDB uses a defined list of specific key values as the basis for each partition. Users can use list partitioning to control how individual rows map to particular partitions. This approach is convenient when the partition keys used to distinguish data sets are not easily sortable, allowing grouping and distribution of data.

- Disperses data into different partitions based on enumerated partition key values.

- The partition boundary for each partition corresponds to the partition key values it can store; only data with partition key values in the current partition belongs to that partition.

- Supports multi-column partition keys.

- Supports specifying a DEFAULT partition; data not belonging to other partitions falls into the DEFAULT partition.

#### Interval Partition

Interval partitioning is a special type of range partition that does not require specifying all data partitions during table creation; users only need to specify the extension partition rule (interval value), during business operations, partitions can be automatically extended.

In addition to the characteristics of range partitions, interval partitions have the following features:

- The partition key is restricted to a single column, and data types can only be numeric or date types.

- When the partition key value of inserted data does not meet the ranges of all current partitions, the interval partition table can automatically create a new partition based on the specified interval value.

- Partitions created at the same time as the table creation remain range partitions, whereas partitions created during business usage are named interval partitions.

- Interval partitions cannot specify MAX VALUE.

## Partition Table

In relational databases, when the volume of table data reaches a certain size, the performance of operations such as data modification, access, and historical data management will be significantly affected.

Partition tables employ partition technology to divide a table into one or more independent partitions, each of which has its own segments, and users can define the properties of these segments independently (e.g., which tablespace to store them in). Different segments and partitions can be managed independently.

By using partition tables, not only is the flexibility of partition tables enhanced, but the performance degradation associated with large volumes of data is also reduced.

## Partition Index

Similar to partition tables, indexes can also employ partition technology. YashanDB classifies partition indexes into local indexes and global indexes based on whether the partition strategy used by the indexes matches that of the table.

### Local Index

An index with a partition strategy consistent with that of the table is called a local index. Local indexes have the same number of partitions and partition boundaries as the table partitions, with each index partition associated with only one underlying table partition. Each index partition can specify segment storage attributes.

In local indexes, YashanDB can automatically synchronize index partitions with their associated table partitions.

An example of creating a local index is as follows:

```sql
CREATE INDEX idx_sales_info_range_1 ON sales_info_range (year,month,branch)
TABLESPACE yashan
INITRANS 3
UNUSABLE
LOCAL (partition p1 tablespace DEFAULT INITRANS 2 ,partition p2,partition p3);
```

### Global Index

Global indexes have a partition strategy that differs from the table's partition strategy; when the table is a partitioned table, the global index adopts a different partition strategy or may not be partitioned at all.

In global indexes, the index segments may point to any table partition or all table partitions.