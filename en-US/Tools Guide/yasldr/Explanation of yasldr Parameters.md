## Command Line Parameters for yasldr

|Parameter Name |Value Range |Parameter Description |
|--------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BATCH_SIZE                    | [1,65535]                           | Controls the maximum number of data rows sent to the server by a single sender thread. This parameter affects the memory resource consumption and execution performance of *yasldr*. The default value is 4032.                                                                                                                                                                    |
| CONTROL_FILE                  | -                                   | The file containing Load Data DML statements.                                                                                                                                                                                                                                                                                        |
| CONTROL_TEXT                  | -                                   | Contains the Load Data DML statements as a text string. When importing, Load Data from CONTROL_FILE is prioritized as the DML control statement; if CONTROL_FILE is not set, CONTROL_TEXT is used instead.                                                                                                                                                          |
| MODE                           | [BASIC,BATCH]                      | Controls the import mode, supporting BASIC and BATCH modes, with the default value being BATCH.<br>* BASIC: Executes with INSERT INTO bound parameters; only supports importing views, GIS columns, XMLTYPE columns, interval partition tables, and duplicated tables in ISC Distributed Cluster Deployment.<br>* BATCH: Executes using BATCH INSERT protocol; currently cannot import range interval partition tables.                                                      |
| PACKET_SIZE<sup>*</sup>      | [65536,524288]                     | The expected packet size for the client (in bytes); syntax-compatible only, with a default value of 131072.                                                                                                                                                                                                                             |
| CONN_POOL_SIZE                | [1,32]                             | Indicates the size of the client connection pool; syntax-compatible only, with a default value of 5.                                                                                                                                                                                                                                                                                       |
| SENDERS                       | [1,128]                            | Specifies the number of sender threads during the import process. If this parameter is not set, it will be calculated based on the DEGREE_OF_PARALLELISM and DECODER_THREAD_TIMES parameters in the LOAD DATA syntax. The default value is 1.                                                                                                            |
| CSV_CHUNK_SIZE                | [16,4096)                          | Controls the granularity (in MB) of splitting the CSV data file during the import process; the default value is 128MB. The system will split the data file according to this parameter and allocate the split files to reader threads for further parsing.                                                                                                                                                                   |
| INLINE_BLOB_FORMAT            | [BINARY,STRING]                   | Controls how BLOB fields in the CSV data file are parsed during the import process; the default value is BINARY.<br>* BINARY: Parses in binary format.<br>* STRING: Parses in string format.                                                                                                                                                                   |
| PROGRESS                      | [NULL,DETAIL]                      | Controls how the import progress is displayed during the import process; the default value is NULL.<br>* NULL: No progress information is displayed during the import process.<br>* DETAIL: Displays the percentage of CSV file reading progress, the number of successfully imported rows, the number of failed rows, and the number of empty rows.                                                                               |
| LOG_PATH                      | -                                   | Specifies the directory to write the log file (yasldr.log). If not specified, the log file will be created in the current execution path, supporting relative paths.                                                                                                                                                                                |
| LOG_LEVEL                     | [OFF,ERROR,WARN,INFO,DEBUG,TRACE] | Controls the log level (yasldr.log) for logging; the default value is INFO.                                                                                                                                                                                                                                                                             |
| SLICE_POLICY                  | [SEQUENCE,RANDOM]                 | Controls the algorithm for distributing file slices among reader threads; the default value is SEQUENCE.<br>* SEQUENCE: Sequential allocation.<br>* RANDOM: Random allocation.                                                                                                                                                             |
| DATE_FORMAT                   | -                                   | Sets the parsing format for date columns in the CSV file during import; the default value is yyyy-mm-dd hh24:mi:ss.                                                                                                                                                                              |
| TIME_FORMAT                   | -                                   | Sets the parsing format for time columns in the CSV file during import; the default value is hh24:mi:ss.ff.                                                                                                                                                                                   |
| TIMESTAMP_FORMAT              | -                                   | Sets the parsing format for timestamp columns in the CSV file during import; the default value is yyyy-mm-dd hh24:mi:ss.ff.                                                                                                                                                                     |
| TIMESTAMP_TZ_FORMAT           | -                                   | Sets the parsing format for timestamp with time zone columns in the CSV file during import; the default value is yyyy-mm-dd hh24:mi:ss.ff tzh:tzm.                                                                                                                                                   |
| CSV_LINE_SIZE                 | [1, 1048576]                      | Controls the maximum length (in KB) of a single row of CSV data during import; the default value is 126KB.                                                                                                                                                                                                                         |
| GIS_SRID                      | [0, INT_MAX]                      | Sets the coordinate system when importing GIS data; the default value is 0.                                                                                                                                                                                                                                                                                        |
| CHARACTER_SET                 | [ASCII,GBK,UTF8,ISO88591,GB18030] | Sets the encoding format of the CSV file during import; matches the client character set by default. When importing lob, this parameter also affects the character set of the lob file and does not support separate settings for the lob file's character set.                         |
| MAX_MEM_SIZE                  | [1, 1024]                          | Sets the maximum memory size (in GB) that can be used during import; the default value is 40GB. In the partition table import scenario, memory usage during the import process increases rapidly as the number of partitions increases. To avoid OOM errors during import, memory usage is estimated based on the number of partitions before the import, with the formula mem_used = reader_count * 8M + (part_count + reader_count)* 8 * (BATCH_SIZE * (max_line_size * 2 + 8 + 24 + 8 + col_count * 32)). If the estimated value exceeds this parameter, the import will fail to start. You can try increasing this parameter, reducing BATCH_SIZE, or using BASIC mode for the import. |
| BIT_FORMAT                    | [DECIMAL, BINARY]                 | Sets the parsing method for bit columns in the CSV file during import, supporting both BINARY and DECIMAL formats, with the default value being BINARY.<br>* DECIMAL: Parses CSV numeric values in decimal format; only valid decimal numbers are allowed.<br>* BINARY: Parses CSV numeric values in binary format; only characters 0 and 1 are allowed.                                                                 |
| DELETE                        | [TRUE,FALSE]                      | Controls whether to delete the original data file after a successful import; the default value is FALSE. Please confirm carefully before setting this parameter to TRUE.                                                                                                                                                                    |
| EMPTY_STRING_AS_NULL          | [TRUE,FALSE]                      | Sets how empty strings in the CSV file are parsed during import; the default value is TRUE, indicating that empty strings are parsed as NULL. When set to FALSE, empty strings will be parsed as non-empty, generally used in MySQL compatibility mode.                                                                                              |
| SENDER_TIMEOUT                 | [0,86400]                         | Sets the timeout duration (in seconds) for sender threads during the import process; the default value is 30. If set to 0, the sender thread will not timeout; if there is a unique constraint on the table, the import may get stuck.                                                                                                                                      |
| PART_SEND_POLICY              | [SQF,HASH,ROUND]                  | Sets the algorithm for distributing data units to sender threads during import; the default value is SQF. SQF indicates shortest queue first, and HASH indicates that the data unit ID is hashed based on the number of sender threads. Using HASH can significantly improve performance when importing partition tables. If the CSV data distribution is extremely uneven, resulting in some partition data being far higher than others, the SQF algorithm should be used.                                                                                                                                               |
| ENABLE_AFFINITY               | [TRUE,FALSE]                      | Sets whether reader threads and senders run with CPU affinity during the import process; the default value is TRUE, indicating that CPU affinity will be applied.                                                                                                                                                                                |
| AFFINITY_SEND                 | [TRUE,FALSE]                      | Sets whether to enable affinity import in YAC deployment; the default value is FALSE. When enabled, regular table objects and level-1 partition objects can leverage affinity to improve import performance in mode=BATCH. Affinity import requires the yasldr operation user to have SELECT_CATALOG_ROLE role privileges. You can query whether objects have affinity properties configured through the ALL_OBJECT_AFFINITIES view.   |
| MOJIBAKE_REPLACE | [TRUE,FALSE] | During data import, whether to tolerate unrecognizable characters in the source data file. The default value is `FALSE`, indicating that no tolerance for unrecognizable characters is applied — if the source byte stream contains characters not supported by the tool's specified character set, import may fail. <br />The tolerance mechanism for unrecognizable characters is as follows:  <br/>1. When character set conversion fails due to unrecognizable characters in the source byte stream, skip 1 byte from the source byte stream and add a `?` character to the target buffer.  <br/>2. If unrecognizable characters still exist, repeat the above step until the end of the source byte stream.  |

## Load Data DML Parameters

### Parameter List

|Parameter Name |Value Range |Parameter Description |
|------------|---|-------------|
| COMMIT_ROWS                    | [1,UINT32_MAX]              | Controls the granularity of commits. The default value is 4096.                     |
| DECODER_THREAD_TIMES           | [1,255]                      | Decides the allocation ratio of threads. The default value is 7.                     |
| DEGREE_OF_PARALLELISM         | [2,256]                      | Specifies the concurrency level of data loading tasks. The default value is 8.       |
| ENABLE_BULK                    | [TRUE,FALSE]                | Specifies whether to use bulk mode for import. The default value is FALSE.            |
| ENABLE_DEDUP                   | [TRUE,FALSE]                | Specifies the conflict handling method for LSC tables. The default value is FALSE.    |
| ERRORS                         | [0,4294967295]               | Specifies the upper limit for fault tolerance during import. The default value is 50. |
| LOB_PATH_RELATIVE              | [TRUE,FALSE]                | Specifies whether the LOB supports absolute paths during file splitting. The default value is FALSE.          |
| LOG                            | -                            | Specifies the generated log file. If this parameter is omitted, a log file with the .log suffix will be generated based on the path and name of the first imported file. |
| NOLOGGING                      | [TRUE,FALSE]                | Specifies whether to turn off table transaction logging during import. The default value is FALSE.                                  |
| NULL_LOB_FORMAT_SIZE           | [0,1]                       | Specifies whether NULL values in LLS import mode support the 4-byte 0xFF representation. The default value is 0, indicating no support.        |
| RUN_LEVEL                      | [SPLIT,SPLIT_TO_PART]       | Specifies the type of task the program performs. Invalid for data import.      |
| SILENT                         | [TRUE,FALSE]                | Specifies whether to generate record files during import, including log files, error data files, and filtered data files. The default value is FALSE.  |
| STATS                          | [TRUE,FALSE]                | Specifies whether to record and generate statistics reports during import. The default value is FALSE. This parameter is used for version compatibility; statistics will always be collected and printed (regardless of the parameter value).        |
| TRIM                          | [LDRTRIM,NOTRIM]            | Specifies whether to trim whitespace in the CSV data file. The default value is LDRTRIM, meaning that leading whitespace and tabs are trimmed.                                |

### Detailed Description

#### COMMIT_ROWS

Determines the frequency of transaction commits. If the value is n, it decodes n data rows from the data file and commits the transaction once. This parameter is invalid when using BULKLOAD for import.

`parameter_value` must be an integer within the range [1,4294967295]; the default value is 4096.

#### DECODER_THREAD_TIMES

Determines the allocation ratio of threads, specifically the ratio of READER threads to SENDER threads. The total of the two types of threads does not exceed DEGREE_OF_PARALLELISM.

When the SENDERS parameter is not set, SENDER_CNT = round(DOP, DECODER_THREAD_TIMES + 1), READER_CNT = MIN(SLICE_CNT, DOP - SENDER_CNT).

`parameter_value` must be an integer within the range [1,255]; the default value is 7.

#### DEGREE_OF_PARALLELISM

Specifies the level of parallelism for the data loading task, limited by the number of CPU cores on the executing machine, with a maximum of four times the number of CPU cores.

`parameter_value` must be an integer within the range [2,256]; the default value is 8, indicating the desired level of parallelism. The system will calculate the actual level of parallelism based on user expectations, hardware environment, data file size, etc., and ensure that the loading task runs concurrently according to the actual value.

> **Note**:
>
> If the import statement specifies the WITH EMBEDDED option and DEGREE_OF_PARALLELISM is set in the range [3,20], *yasldr* will start an additional FileReader thread to handle the pipelined reading of file contents for performance enhancement.

#### ENABLE_BULK

When set to TRUE, *yasldr* will use bulk load mode for import, which is only supported for LSC tables.

When importing duplicated tables, *yasldr* will use basic mode for import, making the ENABLE_BULK flag ineffective.

`parameter_value` must be TRUE or FALSE; the default value is FALSE.

#### ENABLE_DEDUP

Specifies the conflict handling method for LSC tables.

This option must be used in conjunction with the ENABLE_BULK option, and *yasldr* needs to operate in BATCH import mode. `parameter_value` must be TRUE or FALSE; the default value is FALSE.

When set to TRUE, the LSC table will handle unique constraint conflicts arising during import by deduplication. The number of confirmed successful records displayed after import represents the original record count, while the actual record count in the table must be queried manually.

#### ERRORS

Specifies the upper limit for the number of data errors during the import process. When the number of erroneous data rows reaches the limit, the data import will be terminated. In cases of importing multiple files, the number of erroneous data rows is summed.

`parameter_value` must be an integer within the range [0,4294967295]; the default value is 50.

#### LOB_PATH_RELATIVE

Specifies the path type of LOB files during file splitting in outline lob import.

`parameter_value` must be TRUE or FALSE; the default value is FALSE.

- If set to TRUE, only relative paths are supported for LOB files during file splitting. If an absolute path is provided, an error will be raised during execution.
- If set to FALSE, both relative and absolute paths are permitted for LOB files during file splitting. If an absolute path is provided, *yasboot* will not copy the data to remote nodes.

#### LOG

Specifies the generated log file that records the execution information of the data loading tasks, including reasons for data import failure and the number of successfully imported records, related to the imported files specified in the input_file_clause statement. Regardless of whether this parameter is specified, a log file will be generated by default. If omitted, it will generate a log file with the same name as the first imported file in the directory of that file, with the suffix log.

`parameter_value` supports relative and absolute paths and can only specify the file name.

> **Note**:
>
> The parameters related to log file generation during data import in YashanDB are SILENT, STATS, and LOG, and their relationships are as follows:
>
> - When SILENT is TRUE, the STATS and LOG parameters are ineffective, meaning that no import logs, error data files, or filtered data files will be generated.
> - When SILENT is FALSE: Whether STATS is TRUE or FALSE, the log file will always contain statistics.

#### NOLOGGING

When set to TRUE, tables declared with LOGGING in the statement will be set to NOLOGGING for import and restored to LOGGING state post import; when set to FALSE, no operations will be performed on the table's LOGGING status.

If the initial state of the table is LOGGING, it needs to be restored to LOGGING status after import. The mechanism is asynchronous and occurs in the background, and this operation may fail; the result of the conversion will be recorded in the execution log (refer to LOGGING ASYNC action for details).

`parameter_value` must be TRUE or FALSE; the default value is FALSE.

It is only recommended to set the NOLOGGING attribute in data migration scenarios, while you should take the following into account:

- The primary/standby environment should not enable NOLOGGING; the standby database should be set up after data import is completed.
- After using NOLOGGING import, performing a full checkpoint can ensure data persistence; otherwise, it may lead to data loss after a crash.
- When the table property is NOLOGGING, and a crash occurs, upon restart, it will be marked as corrupted and can only be recovered by truncating the table, requiring the aforementioned import process to be rerun to restore data.
- If a transaction fails, all NOLOGGING tables that have performed insert operations within the transaction will be marked as corrupted.
- The bulk load mode import of LSC tables with NOLOGGING attributes is unaffected by the NOLOGGING setting (performance remains unchanged and failures will not be marked as corrupted).
- When a table is NOLOGGING, update and delete operations cannot be performed. This attribute should be quickly modified back to logging after completion of the import.
- Modifying the table status to NOLOGGING is considered a DDL operation and will lock the table. Concurrent execution of DML statements may cause DML statement failures.
- When a table is NOLOGGING, or imported using NOLOGGING, constraint violations may result in fault tolerance failures, specifically affected by the position of the violated data among all data.

##### NULL_LOB_FORMAT_SIZE

When the value is 1, it represents that a specific LOB type's NULL value is organized as 4B full F; this mode is only used for LLS imports. When the value is 0, it indicates the normal representation of NULL values for LOB types, organized as 8B full F; this is a universal representation for LOB type NULL values.

`parameter_value` can only be 0 or 1; other values are invalid, and the default value is 0.

##### RUN_LEVEL

This option is used to specify the type of task performed by the program. When set, it specifies the task type as file splitting; if not set, the default is data import task and won't perform file splitting.

**SPLIT Mode**

This mode is used for executing the task of splitting CSV files, without executing data import.

For large data volumes (greater than 500G), it is recommended to first execute this command to split the CSV file before importing.

> **Note**: 
>
> Duplicated tables do not support splitting after connecting to CN but support splitting according to partition after connecting to DN.
> The built-in *yasboot* tool of YashanDB can achieve one-click splitting and importing of CSV files; please refer to the [Data Import Command](../yasboot/Introduction to yasboot Command/yasboot load) section for details.

CSV files will be split according to the partition key. When a directory is specified, the generated path for split files will point to the specified directory; if no directory is specified, the generated path for split files will be the directory of the first file in INFILE.

***Example***

```bash
# Contents of area.csv:
6|load|601|loadbranch|6
7|load|701|loadbranch|7

$ yasldr sales/sales@127.0.0.1:1688 batch_size=4032 control_text="'LOAD DATA OPTIONS(RUN_LEVEL=SPLIT,DEGREE_OF_PARALLELISM=2) INFILE '/home/yasdb/area.csv' FIELDS TERMINATED BY '|' OPTIONALLY ENCLOSED BY '\"' APPEND INTO TABLE area(area_no,area_name,dhq) directory '/home/yasdb/file''"
```

**SPLIT_TO_PART Mode**

This mode is used to execute the CSV file split task according to partitions, without executing data imports.

File generation paths:

- In Standalone Deployment, the generation path is the same as SPLIT mode.
- In ISC Distributed Cluster Deployment, when splitting non-partition tables, the path is the same as SPLIT mode.
- In ISC Distributed Cluster Deployment, when the splitting object is multiple partition tables, a folder named with `node IP:port` will be generated under the specified directory or the directory of the first file in INFILE, and the split files will be generated within that folder based on the partition. 

#### SILENT

Specifies whether to generate record files during the import process, including log files, error data files, and filtered data files. When set to TRUE, it indicates that no record files will be generated; when FALSE, record files will be generated.

`parameter_value` must be TRUE or FALSE; the default value is FALSE.

#### STATS

Specifies whether to print statistics during the import process. This parameter is only used for version compatibility; statistics will always be collected and printed regardless of this parameter. The output log of printed statistics will be saved in the path of the imported data file and will have the same name as that data file, with a log file format.

`parameter_value` must be TRUE or FALSE.

The specific statistics printed include:

- CONTROLLER statistics: Display the number of READER and SENDER threads, parsing time, and the time for preparing the execution environment.
- READER statistics: Display the number of IOs, IO time, number of decoded rows, maximum row width, average row width, total time for reading rows, total decoding time, total partition calculation time, total conversion time, total waiting time for partitions to be available, total sending queue time, and total runtime of threads.
- SENDER statistics: Display the number of sends, total sending time, maximum time per send, number of commits, total commit time, total time for obtaining send units, total conversion time, and total runtime of threads.
- NODE statistics (only in ISC Distributed Cluster Deployment): Display the number of rows sent to the node, number of sends, total sending time, maximum time per send, number of commits, total commit time, and node address information.

The specific description of the statistics output can be found in the [User Guide for yasldr](User Guide for yasldr) under the statistics section.

#### TRIM

Indicates how whitespace in the CSV data file is handled. LDRTRIM indicates that leading whitespace and tabs for data columns will be trimmed, while NOTRIM means no trimming will occur for leading whitespace and tabs. For data with enclosing characters, the characters inside the enclosing will be treated as data and not affected by this parameter.

`parameter_value` must be LDRTRIM or NOTRIM; the default value is LDRTRIM.
