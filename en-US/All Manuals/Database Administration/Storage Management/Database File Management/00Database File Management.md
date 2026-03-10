YashanDB includes the following important physical files:

|File Type |File Description |File Path (Default) |
|--------------------|-------------------|-------------------|
| [Configuration Parameter File](Configuration Parameter File and Password File Management) | A text file containing memory component size, listening port, character set, data block size, control file, and other instance configuration information of the database instance. | $YASDB_DATA/config/yasdb.ini |
| [Password File](Configuration Parameter File and Password File Management) | A text file that stores the encrypted password of the sys user, which is required for the sys user to log in to the database. | yasdb.pwd<br/> The path is controlled by the PASSWORD_FILE parameter. |
| [Control File](Control File Management) | A binary file that stores critical information about the database, making it one of the core files of the database. | $YASDB_DATA/dbfiles/ctrl* |
| [Data File](Data File Management) | A binary file that stores data for the database, which can only be read and written by the database. | Standalone, ISC Distributed Cluster Deployment: $YASDB_DATA/dbfiles<br/> YAC/Distributed Cluster Deployment: +DG0/dbfiles |
| [Redo Log File](Redo Log File Management) | A binary file for redo logs, used for the persistence of redo logs. | Standalone, ISC Distributed Cluster Deployment: $YASDB_DATA/dbfiles<br/> YAC/Distributed Cluster Deployment: +DG0/dbfiles |
| [Archive Log Files](Archive Log File Management) | Binary files created after redo logs are archived when [archiving mode](../../实例管理/归档管理) is enabled. Archive log files can be used for data recovery and can also be applied to the standby database in the primary standby database to restore data on the primary database. | $YASDB_DATA/archive |

Each file type has its specific functionality and role. The absence of any file will affect the normal use of the database. For example, without the parameter file, the database instance cannot start; without the password file, the sys user cannot log in; and without the control file, the database cannot start in the mount state.

>**Warn**:
>
>**Manual changes** to the database control files, data files, and redo files are **prohibited**, including deletion, movement, privilege changes, etc. Otherwise, it may cause the database to fail to start or run abnormally, resulting in severe consequences.