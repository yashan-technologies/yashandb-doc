This view displays YFS disk information.

|Field |Type |Description |
|-------|------|------|
|  ID             | INTEGER  | The unique ID of the disk within the associated DiskGroup      |
| GLOBAL_ID      | INTEGER  | The global ID of the disk                               |
| NAME           | VARCHAR(32) | The name of the disk              |
| GROUP_NUMBER    | INTEGER  | The ID of the DiskGroup to which the disk belongs               |
| FAILGROUP_ID   | INTEGER  | The ID of the FailureGroup to which the disk belongs             |
| FAILGROUP_LABEL| VARCHAR(32) | The name of the FailureGroup to which the disk belongs        |
| MOUNT_STATUS    | VARCHAR(16) |  The status of the disk  <br/> * NORMAL: The disk is in normal status <br/> * OFFLINE_SYNC: A temporary state during the disk online process — the disk is writable but not readable. After online completion, the disk becomes normal  <br/> * OFFLINE: The disk is offline  |
| REDUNDANCY     | VARCHAR(16) | The redundancy level of the disk's associated DiskGroup  <br>\* External: YFS does not provide data redundancy replicas <br>\* Normal: The system DiskGroup provides [2,3] replicas (the exact number depends on the number of FailureGroups) under this configuration. The data DiskGroup provides [2,3] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 2 user data replicas under this configuration  <br>\* High: The system DiskGroup provides 5 replicas under this configuration. The data DiskGroup provides [3,5] YFS metadata replicas (the exact number depends on the number of FailureGroups) + 3 user data replicas under this configuration       |
| PARTNERS       | VARCHAR(161) | The list of partner disks for the disk, with multiple IDs separated by spaces |
| TOTAL_MB       | BIGINT   | The total capacity of the disk (in MB)                         |
| FREE_MB        | BIGINT   | The currently available capacity of the disk (in MB)             |
| PATH           | VARCHAR(32) | The disk path                                |
| READS          | BIGINT   | Total number of reads performed by the current instance on this disk           |
| WRITES         | BIGINT   | Total number of writes performed by the current instance on this disk            |
| BYTES_READ     | BIGINT   | Total number of bytes read by the current instance from this disk        |
| BYTES_WRITTEN  | BIGINT   | Total number of bytes written by the current instance to this disk         |
| READ_TIME      | BIGINT   | Total read duration for this disk by the current instance (in μs)              |
| WRITE_TIME     | BIGINT   | Total write duration for this disk by the current instance (in μs)    |
