The C Driver file system function provides a set of file operation interfaces that support client programs to perform directory and file operations on unstructured data files within the database. This function uses a client-server interaction model and implements file operation requests and responses through the CMD_FILE command.

Before using file system functions, you need to create a volume through the [DBMS_FS](../../../../PL Reference Manual/Built-in Advanced PL Packages/DBMS_FS) advanced package, and then set the current volume through `yacFsSetCurrentVolume`. All file operations (creation, deletion, reading, writing, etc.) are performed under the currently set volume.

|  Interface Name| Description|
|-------------------|--------------------------------------|
| [yacFsSetCurrentVolume](yacFsSetCurrentVolume) | Sets the volume used by the current connection |
| [yacFsGetCurrentVolume](yacFsGetCurrentVolume) | Gets the volume used by the current connection |
| [yacFsMkDir](yacFsMkDir) | Creates a specified directory under the current volume |
| [yacFsRmDir](yacFsRmDir) | Deletes a specified directory under the current volume |
| [yacFsCreateFile](yacFsCreateFile) | Creates a specified file under the current volume |
| [yacFsRmFile](yacFsRmFile) | Deletes a specified file under the current volume |
| [yacFsFileExist](yacFsFileExist) | Checks whether a specified file exists under the current volume |
| [yacFsFileStat](yacFsFileStat) | Gets file information under the current volume |
| [yacFsFileRead](yacFsFileRead) | Reads file data from a specified position under the current volume |
| [yacFsFileWrite](yacFsFileWrite) | Writes data to a specified file under the current volume |
| [yacFsListDir](yacFsListDir) | Lists files or directories under a specified directory of the current volume |

