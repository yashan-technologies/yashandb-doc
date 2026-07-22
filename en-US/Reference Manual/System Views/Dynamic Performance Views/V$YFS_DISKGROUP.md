This view displays information about the YFS DiskGroup.

|Field |Type |Description |
|-------|------|------|
|   ID        | INTEGER     | The ID of the DiskGroup                                |
| NAME      | VARCHAR(32) | The name of the DiskGroup            |
| TYPE      | VARCHAR(32) | The type of the DiskGroup <br>\* SYSTEM: The system DiskGroup, used to store YFS's own metadata <br>\* USER: The data DiskGroup, used to store business data files |
| AU_SIZE   | INTEGER     | The size of the AU (in bytes), which is the smallest unit for YFS to allocate disk space                |
| BLOCK_SIZE| INTEGER     | The size of the file data block (in bytes)      |
| REDUNDANCY| VARCHAR(16) | The redundancy level of the DiskGroup  <br>\* External: YFS does not provide data redundancy replicas <br>\* Normal: The system DiskGroup provides [2,3] replicas (the exact number depends on the number of FailureGroups) under this configuration. The data DiskGroup provides [2,3] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 2 user data replicas under this configuration  <br>\* High: The system DiskGroup provides 5 replicas under this configuration. The data DiskGroup provides [3,5] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 3 user data replicas under this configuration |
| STATE     | VARCHAR(16) | The status of the DiskGroup <br>\* BROKEN: DiskGroup is faulty <br>\* MOUNTED: DiskGroup is in normal state <br>\* DISMOUNTED: DiskGroup is not mounted |
| TOTAL_MB  | BIGINT      | The total capacity of the DiskGroup (in MB)              |
| FREE_MB   | BIGINT      | The available space in the DiskGroup (in MB)           |
| USABLE_FILE_MB | BIGINT | The available file size in the DiskGroup (in MB), i.e., the available space minus the capacity required for multi-replication   |
