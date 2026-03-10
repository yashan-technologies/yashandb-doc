## Automatic Diagnosis Repository

YCS has established an automatic diagnosis repository for YAC, which collects information such as process stack traces before system failures and crashes, and dumps diagnostic data into the automatic diagnosis repository. This proactive diagnostic data is similar to the data collected by an aircraft's "black box" flight recorder.

The automatic diagnosis repository is a file-based repository used to store diagnostic data for YAC. Its directory structure is as follows (default location under the YASCS_HOME directory, path and name cannot be modified):

```shell
$YASCS_HOME/diag/blackbox
```

|Subdirectory Name |Content |
| ---------- | -------------------- |
| blackbox           | Stores diagnostic data of the black box |

The automatic diagnosis repository functionality is enabled by default and cannot be manually turned off.

> **Note**: 
>
> The file data under the blackbox directory is internal structure data of YashanDB. Please package this diagnostic data and contact our technical support for processing in a timely manner.