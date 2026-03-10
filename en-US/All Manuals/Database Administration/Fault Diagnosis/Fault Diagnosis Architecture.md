The fault diagnosis architecture consists of multiple components, including the automatic diagnosis repository, runtime logs, and alarm logs. For runtime logs and alarm logs, please refer to the relevant descriptions in [Log Management](../Log Management/00Log Management).

## Automatic Diagnosis Repository

The automatic diagnosis repository is a file-based repository used to store database diagnostic data, with the following directory structure:

![](./image/adr_1_.png)

|Subdirectory Name |Purpose and Description |
| ---------- | ----------------------------------------------------------- |
| hm                 | Stores reports from [Health Monitor](Health Monitor)                  |
| metadata           | Stores metadata files for the automatic diagnosis repository (mainly includes incident, problem, etc.) |
| blackbox           | Stores data from [automatically captured diagnostic data](Dump.html#blackbox) ("black box"). This directory does not need to be created manually and will be automatically created along with the paths and files before a database process crash.                                         |
| trace              | Stores automatically generated trace logs, [Dump](Dump) data, and the output of [Trace events](Trace)                  |

The automatic diagnosis repository is located by default in the $YASDB_DATA directory and can be adjusted through the DIAGNOSTIC_DEST parameter.

> **Note**: 
>
> After updating the directory location, if you need to retain and continue using historical data files, you must manually copy the subdirectories and files to the new path; otherwise, the database will automatically create new subdirectories and relevant files.

The functionality of YashanDB's automatic diagnosis repository is enabled by default. If you wish to disable this functionality, you can configure the parameter DIAG_ADR_ENABLED and restart the database for it to take effect. After disabling, automatic inspections will no longer be triggered, including file monitoring, reactive health checks, and monitoring of synchronized standby databases under maximum protection mode.

```sql
ALTER SYSTEM SET DIAG_ADR_ENABLED = FALSE SCOPE = SPFILE;
```

## Trace Files

In addition to the trace logs generated automatically in the background, the output results of [Dump](Dump) and [Trace events](Trace) will also be saved as trace files.

YashanDB supports managing the maximum size of trace files from the following dimensions to prevent unnecessary storage space waste or performance degradation.

- The size of a single trace file: specified by the MAX_TRACE_FILE_SIZE parameter.

    ```sql
    ALTER SESSION SET MAX_TRACE_FILE_SIZE = '10M';
    ```

- The total capacity of the trace directory: specified by the MAX_TRACE_DIR_SIZE parameter. When the total size of the files in the directory exceeds the threshold, earlier files will be automatically cleared. Do not configure other file paths to be the same as the trace path.

    ```sql
    ALTER SESSION SET MAX_TRACE_DIR_SIZE = '100M';
    ```
