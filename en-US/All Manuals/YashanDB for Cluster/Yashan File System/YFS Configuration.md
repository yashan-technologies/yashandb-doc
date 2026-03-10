YFS obtains configuration parameters through a parameter file. Users can modify the parameter file and restart the YFS service to apply the changes.

This document will introduce the configuration parameters open in YFS. Please choose the appropriate items and values based on the descriptions to meet the operational requirements of YFS in your environment.

## Parameter File

The name of the YFS parameter file is `yasfs.ini`, which cannot be changed and must be located in the following directory structure on each server:

```text
# Please ensure that the YASCS_HOME contains at least the following directory structure:
├── config
│   └── yasfs.ini
├── instance
└── log
```

## Parameter Items

### LOG\_LEVEL 

*   Parameter Type: String

*   Default Value: INFO

*   Value Range/Format: [OFF,FATAL,ERROR,WARN,INFO,DEBUG,TRACE,ALL]

*   Parameter Description: The log level of YFS during runtime.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RECY\_INTERVAL 

*   Parameter Type: Numeric

*   Default Value: 0

*   Value Range/Format: [0,4294967295]

*   Parameter Description: The time interval for file deletion to resource recycling in YFS, measured in seconds. When space is insufficient, files that do not meet the time interval requirements will also be forcibly recycled.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### PARSER\_STACK\_SIZE

*   Parameter Type: Numeric

*   Default Value: 8M

*   Value Range/Format: [1K,1G]

*   Parameter Description: Specifies the stack space used by YFS to parse disk group management statements. This configuration is positively correlated with the number of failgroups and disks managed within a single disk group; approximately 1K of stack space is required for every 5 disks. Stack space should be adequately reserved based on business considerations.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### SHM\_POOL\_SIZE

*   Parameter Type: Numeric

*   Default Value: 2G

*   Value Range/Format: [64M,510G]

*   Parameter Description: Specifies the upper limit of shared memory that can be used during the operation of the YFS instance, primarily for caching metadata of files, diskgroups, and disks, as well as session pool management. The size is related to factors such as file size and quantity, the number of diskgroups, the number of disks, and the maximum number of sessions.  The estimation formula for memory usage per file is:  `File size / 38K`, where if the estimated value for a single file is not an integer multiple of Au Size, it needs to be rounded up to the nearest multiple. The overall memory estimation formula is: `All file memory usage + (diskgroup count * 10M) + (disk count * 4K) + (max session count * 18K)`. If the estimated value is not an integer multiple of AuSize, it needs to be rounded up to the nearest multiple. Generally, it is recommended to configure at least 2GB. A configuration that is too small may cause failures in creating files, diskgroups, and disks. This parameter can be adjusted online. Make sure this configuration is consistent across all YFS instances, or metadata synchronization issues may arise between instances.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### SYS\_AREA\_SIZE 

*   Parameter Type: Numeric

*   Default Value: 32M

*   Value Range/Format: [32M, infinity)

*   Parameter Description: Specifies the upper limit of local memory that can be used during the operation of the YFS instance, used for caching directory structures. Its size is related to the total number of directories and files in YFS. The estimation formula is: `(Total number of directories + total number of files) * 2K + 2M`. A configuration that is too small may lead to directory or file creation failures. This parameter can also be adjusted online. Ensure this configuration is consistent across all YFS instances to prevent metadata synchronization issues.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### YFS\_PACKET\_SIZE 

*   Parameter Type: Numeric

*   Default Value: 6K

*   Value Range/Format: [6K, infinity)

*   Parameter Description: Cache size for YFS communication with the client, typically the default value is sufficient.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RECY\_TASK\_INTERVAL 

*   Parameter Type: Numeric

*   Default Value: 3600

*   Value Range/Format: [600,18446744073709551615]

*   Parameter Description: The time interval for YFS to trigger resource recycling tasks, measured in seconds.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RECY\_UPPER\_THRESHOLD 

*   Parameter Type: Numeric

*   Default Value: 80

*   Value Range/Format: [0,100]

*   Parameter Description: The upper percentage threshold for triggering YFS resource recycling on the disk.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### RECY\_LOWER\_THRESHOLD 

*   Parameter Type: Numeric

*   Default Value: 20

*   Value Range/Format: [0,100]

*   Parameter Description: The percentage that triggers the stop of resource recycling during YFS resource recycling process.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### YFS\_FRA\_ENABLE 

*   Parameter Type: Boolean

*   Default Value: TRUE

*   Value Range/Format: [TRUE|FALSE]

*   Parameter Description: Indicates whether to enable YFS fast recovery area functionality.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### MAX\_SESSIONS 

*   Parameter Type: Numeric

*   Default Value: 1024

*   Value Range/Format: [1024,60000]

*   Parameter Description: The maximum number of sessions supported by YFS. If the configured value is below the minimum threshold, it will be automatically reset to the minimum value of 1024.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### YFS\_DISKSTRING 

*   Parameter Type: String

*   Default Value: /dev/yfs

*   Value Range/Format: Disk path 1[, disk path 2, …, disk path n]

*   Parameter Description: YFS scans the disks in this path upon startup to build disk group information. Disk paths support the "*" wildcard, multiple paths should be separated by ",". The total length must not exceed 255 bytes. The paths configured with this parameter must encompass all disk paths of the SYSTEM disk group.

*   Immediate Effect on Modification: Yes

*   Session-Level Parameter: No

*   Read-Only Parameter: No

### DATA\_STRIPING 

*   Parameter Type: Boolean

*   Default Value: TRUE

*   Value Range/Format: [TRUE|FALSE]

*   Parameter Description: Indicates whether to enable YFS striping functionality for YashanDB data files. Adjusting this parameter will only affect new data files created after the parameter modification; it will not impact existing files.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### REDO\_STRIPING 

*   Parameter Type: Boolean

*   Default Value: FALSE

*   Value Range/Format: [TRUE|FALSE]

*   Parameter Description: Indicates whether to enable YFS striping functionality for YashanDB online logs and archive log files. Adjusting this parameter will only affect new online logs and archive log files created after the parameter modification; it will not impact existing files.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes

### MAX\_IO\_RETRY\_TIME 

*   Parameter Type: Numeric

*   Default Value: 512

*   Value Range/Format: [512, infinity)

*   Parameter Description: IO timeout duration. If a disk failure causes an IO timeout, it will report the disk failure to the YFS server for processing. The unit is milliseconds.

*   Immediate Effect on Modification: No

*   Session-Level Parameter: No

*   Read-Only Parameter: Yes