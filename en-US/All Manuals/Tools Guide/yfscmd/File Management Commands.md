YFS uses file operation commands similar to those of general file systems to manage YFS directories and files.

## cat

This command prints the YFS file in binary mode to standard output.

> **Note**: 
> 
> This command is part of the laboratory feature.
>
> YFS files are mostly binary data, direct output of file contents is not recommended as it may produce garbled output and cause abnormal shell sessions.
> 
> This command can be used in non-interactive mode to redirect the file data stream to other tools for processing.

Command format:

```bash
cat  filename
```

## cd

This command is used to switch the working directory. Command format:

```shell
cd path
```

**path**

The new working directory path, can be relative or absolute, must be YFS path, and cannot be omitted.

## cin

This command is used to write a data stream to a YFS file.

> **Warn**:
> 
> This command is part of the laboratory feature and is a high-risk operation.
> 
> This command may change the size of the YFS file and lose data. **Do not use in production environments** and do not use for operating database files, business data, etc.

Command format:

```bash
cin [-e] [-s size] filename
```

- -e: Automatically extend the file, default is no extension.
- -s: The offset to start writing, default is 0. `1024`, `1K`, `1M`, `1G` are all valid size formats.

This command reads standard input in binary mode and writes to the YFS file. The target file must exist. The length of the data written is determined by the standard input stream.

If `-e` is not specified, the target file will not automatically extend, ensure that the written data does not exceed the end of the file, otherwise an error will occur. If `-e` is specified, the file will automatically extend by 512 bytes with data writing, when the input stream length does not meet the 512-byte alignment, the unwritten position data is undefined.

Please use this command in non-interactive mode combined with pipe, interactive mode use is not supported.

```bash
# Please execute in Linux shell in *yfscmd* command line mode, and use `|` pipe to input data stream for yfscmd cin
# Write 10MB of data filled with 0 from DG0/data file at 1K, file will auto-extend
$ dd if=/dev/zero bs=1M count=10 | yfscmd cin -e -s 1K DG0/data
10+0 records in
10+0 records out
10485760 bytes (10 MB) copied, 0.161797 s, 64.8 MB/s
```

## cp

This command is used to copy files or recursively copy directories. Command format:

```shell
cp src dst
```

**src** 

Source path, which can be a file or directory in YFS or local file system, cannot be omitted.

**dst**

Destination path, which can be a file or directory in YFS or local file system, cannot be omitted, and dst must not exist.

Unlike the cp command in Linux Shell, this cp command will not infer the dst path, the user needs to explicitly specify the new file or folder. For example, when running `cp 1.txt newdir/`, if the newdir directory does not exist, it will create a file named newdir, if the newdir directory exists, it will report an error. Therefore, executing this cp command should clearly indicate the dst path, for instance `cp 1.txt newdir/2.txt`.

*yfscmd* supports copying in the following directions:

- YFS to local file system.
- YFS to YFS.
- Local file system to YFS.

> **Note**: 
>
> To perform copy from local file system to local file system, please use the Linux Shell `cp` command.

*yfscmd* determines the type of file system of the parameters by the root directory symbols in the path:

- `+`: YFS path, all relative paths in *yfscmd* will be converted to YFS paths.
- `/`: Local file system path.

Since all relative paths in *yfscmd* will be converted to YFS paths, to specify a parameter as a local file system path, it must be designated in absolute path format.

For example, `/local/fs/path` will be recognized as a local file system path, while `file` or `+DG0/file` will be recognized as a YFS path.

## file

This command is used to view YFS file metadata.

> **Note**: 
> 
> This command is part of the laboratory feature.

Command format:

```bash
file filename
```

***Example***
```bash
YFSCMD >  file data
fileCtrl = {
    blockHead = {
        checksum     = 0
        changeNum    = 40
        latch.status = 0
        blockId      = 257
        lsn          = 14
        blockSize    = 4096
        type         = 4
    }
    fd        = 257
    size      = 1048576
    auCnt     = 2
    dataCpyCnt= 1
    metaCpyCnt= 1
    createTime= 1721329555179151
    deleteTime= 0
    strpwdth  = 0
    redundancy= 0
    used      = 1
    type      = 0
    count     = 2
    indirAuBlocks= 0
}
```

## help

This command is used to display help information. Command format:

```shell
help [cmd]
```

**cmd**

*yfscmd* subcommand, can be omitted. If omitted, a list of subcommands will be displayed.

## ls

This command is used to print a list of files. Command format:

```shell
ls [-l] [-d] [-s] [path]
```

- -l/s: long format, display detailed information, if the current directory is the root directory, the query result is the same as the command `lsdg`.
- -d: if the parameter is a directory, display the directory's own details instead of the details of the contents of the directory.

**path**

Prints the file list under this path. It can be omitted, in which case it defaults to the current working directory.

## md5sum

This command calculates the md5sum of a YFS file.

> **Note**: 
> 
> This command is part of the laboratory feature and depends on the Linux system `md5sum` tool. If the system does not have this tool installed, the md5sum command will report an error.

Command format:

```bash
md5sum filename
```

***Example***
```bash
YFSCMD >  md5sum DG0/data
f1c9645dbc14efddc7d8a322685f26eb *-
```

## mkdir

This command is used to create a directory. Command format:

```shell
mkdir dir
```

**dir**

The name of the directory to be created, must not conflict with existing file objects, cannot be omitted.

To create a directory in another working directory, specify the target directory path, only YFS paths are supported.

## mv

This command is used to rename and move files or directories.

> **Warn**:
> 
> High-risk operation, do not use this command for operating database files, business data, etc. Do not use this command in production environments.
>
> If there are requirements for data backup, please use the backup and recovery component of YashanDB.

Command format:

```shell
mv src dst
```

**src**

Original file name or path, the path must be a YFS path, cannot be omitted.

**dst**

New file name or path, the path must be a YFS path, and it must not duplicate an existing file object, cannot be omitted.

## pwd

This command is used to display the current working directory. Command format:

```shell
pwd
```

## rm

This command is used to delete directories or files.

> **Warn**:
> 
> High-risk operation, do not use this command for operating database files, business data, etc. Do not use this command in production environments.
>
> To delete database files, please use the corresponding tablespace and archive management commands of YashanDB.

Command format:

```shell
rm [-r] path
```

- -r: If path is a directory, delete recursively.

**path**

The name of the directory or file to be deleted, cannot be omitted. For non-empty directories, the `-r` parameter must be specified for recursive deletion.

## touch

This command creates a file in YFS with a size of 0.

> **Note**: 
> 
> This command is part of the laboratory feature.

Command format:

```bash
touch filename
```

## truncate

This command adjusts the size of the YFS file. When extending the file, the data in the newly extended area is undefined; when shrinking the file, the trailing data is discarded.

> **Warn**:
> 
> This command is part of the laboratory feature and is a high-risk operation.
> 
> This command may change the size of the YFS file and lose data. **Do not use in production environments** and do not use for operating database files, business data, etc.
> 
> To adjust the size of data files, please use YashanDB commands.

Command format:

```bash
TRUNCATE [-q] -s size filename
```

- -q: Quiet mode, does not prompt data risks.
- -s: Adjusts the file size, `1024`, `1K`, `1M`, `1G` are valid size formats.

***Example***
```bash
YFSCMD >  truncate -s 1M data
Resize data from 10.00MB to 1.00MB? May lost data!
[Y/n] Y
YFSCMD >  ll data
Type Time                 Size     Space    Name
FILE 2024-07-18 07:05:55  1.00MB   2.00MB   data
```

## vim

This command is used to edit YFS files.

> **Warn**:
> 
> This command is part of the laboratory feature and is a high-risk operation.
> 
> This command depends on the Linux system `vim`, and using this command may change the size of the YFS file and lose data. **Do not use in production environments** and do not use for operating database files, business data, etc.

Command format:

```bash
vim filename
```

This command only accepts one YFS file path.

The file to be edited will be copied to a temporary file in the local `/tmp` directory. After editing, you will need to agree to overwrite the original YFS file with the modified file. If you cancel at this time, all modifications will be discarded, and the original file in YFS will remain unchanged.

Since this command will involve two copies between YFS and the local file system, if the file to be edited is large, the export time will be long, and it is not recommended to edit very large files.

If the file size changes after editing, *yfscmd* will automatically pad it with 0 to a multiple of 512 bytes in size.

```bash
YFSCMD >  vim 1.txt
your temp file here: /tmp/yfs-edit.IOVZ3F
cp OK: YFS:+DG0/1.txt --> SYS:/tmp/yfs-edit.IOVZ3F
# Entering vim interface, edit and then save and exit
file +DG0/1.txt exists, overwrite?
[Y/n] Y
overwrite +DG0/1.txt .
Warning: File size 13 not aligned to 512, auto resize.
cp OK: SYS:/tmp/yfs-edit.IOVZ3F --> YFS:+DG0/1.txt
```
