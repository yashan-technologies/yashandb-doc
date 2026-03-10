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

*   Parameter type: String

*   Default value: INFO

*   Value range/format: [OFF,FATAL,ERROR,WARN,INFO,DEBUG,TRACE,ALL]

*   Parameter description: The log level of YFS during runtime.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: No

### RECY\_INTERVAL 

*   Parameter type: Numeric

*   Default value: 0

*   Value range/format: [0,4294967295]

*   Parameter description: The time interval for file deletion to resource recycling in YFS, measured in seconds. When space is insufficient, files that do not meet the time interval requirements will also be forcibly recycled.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### PARSER\_STACK\_SIZE

*   Parameter type: Numeric

*   Default value: 8M

*   Value range/format: [1K,1G]

*   Parameter description: Specifies the stack space used by YFS to parse disk group management statements. This configuration is positively correlated with the number of failgroups and disks managed within a single disk group; approximately 1K of stack space is required for every 5 disks. Stack space should be adequately reserved based on business considerations.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: No

### SHM\_POOL\_SIZE

*   Parameter type: Numeric

*   Default value: 2G

*   Value range/format: [64M,510G]

*   Parameter description: Specifies the upper limit of shared memory that YFS can use. Shared memory will be used for YFS metadata buffers; please ensure consistent configuration across all servers. Actual shared memory usage is positively correlated with the maximum number of sessions, disk groups, failure groups, disks, as well as the number, size of files; the estimated formula for value is `ceil(number of configured sessions/2k) * 32M + number of files * 3 * AU_size + 32M`. When shared memory is insufficient, YFS will report an error. During runtime, the parameter value can be increased online using the `yfscmd` tool, but it cannot be decreased.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### SYS\_AREA\_SIZE 

*   Parameter type: Numeric

*   Default value: 32M

*   Value range/format: [32M, infinity)

*   Parameter description: Specifies the upper limit of buffer size that YFS can use. This buffer will be used to store YFS directory metadata; please ensure consistent configuration across all servers. The buffer is positively correlated with the number of files and directories; the estimated formula for value is `(ceil(total number of files and directories / 1K) + 32)M`. When the buffer is insufficient, YFS will report an error. The buffer can be increased online using `yfscmd`, but it cannot be decreased.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### YFS\_PACKET\_SIZE 

*   Parameter type: Numeric

*   Default value: 6K

*   Value range/format: [6K, infinity)

*   Parameter description: Cache size for YFS communication with the client, typically the default value is sufficient.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: No

### RECY\_TASK\_INTERVAL 

*   Parameter type: Numeric

*   Default value: 3600

*   Value range/format: [600,18446744073709551615]

*   Parameter description: The time interval for YFS to trigger resource recycling tasks, measured in seconds.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: No

### RECY\_UPPER\_THRESHOLD 

*   Parameter type: Numeric

*   Default value: 80

*   Value range/format: [0,100]

*   Parameter description: The upper percentage threshold for triggering YFS resource recycling on the disk.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### RECY\_LOWER\_THRESHOLD 

*   Parameter type: Numeric

*   Default value: 20

*   Value range/format: [0,100]

*   Parameter description: The percentage that triggers the stop of resource recycling during YFS resource recycling process.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### YFS\_FRA\_ENABLE 

*   Parameter type: Boolean

*   Default value: TRUE

*   Value range/format: [TRUE|FALSE]

*   Parameter description: Indicates whether to enable YFS fast recovery area functionality.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: Yes

### MAX\_SESSIONS 

*   Parameter type: Numeric

*   Default value: 1024

*   Value range/format: [1024,65535]

*   Parameter description: The maximum number of sessions supported by YFS.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: Yes

### YFS\_DISKSTRING 

*   Parameter type: String

*   Default value: /dev/yfs

*   Value range/format: Disk path 1[, disk path 2, …, disk path n]

*   Parameter description: YFS scans the disks in this path upon startup to build disk group information. Disk paths support the "*" wildcard, multiple paths should be separated by ",". The total length must not exceed 255 bytes. The paths configured with this parameter must encompass all disk paths of the SYSTEM disk group.

*   Immediate effect on modification: Yes

*   Session-level parameter: No

*   Read-only parameter: No

### DATA\_STRIPING 

*   Parameter type: Boolean

*   Default value: TRUE

*   Value range/format: [TRUE|FALSE]

*   Parameter description: Indicates whether to enable YFS striping functionality for YashanDB data files. Adjusting this parameter will only affect new data files created after the parameter modification; it will not impact existing files.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: Yes

### REDO\_STRIPING 

*   Parameter type: Boolean

*   Default value: FALSE

*   Value range/format: [TRUE|FALSE]

*   Parameter description: Indicates whether to enable YFS striping functionality for YashanDB online logs and archive log files. Adjusting this parameter will only affect new online logs and archive log files created after the parameter modification; it will not impact existing files.

*   Immediate effect on modification: No

*   Session-level parameter: No

*   Read-only parameter: Yes

### MAX\_IO\_RETRY\_TIME 

*   Parameter type: Numeric

*   Default value: 512

*   Value range/format: [512, infinity)

*   Parameter description: IO timeout duration. If a disk failure causes an IO timeout, it will report the disk failure to the YFS server for processing. The unit is milliseconds.

*   Immediate effect on modification: No

*   Session-level parameter: No