RECOVER is used for consistent recovery of tablespace object files.

```ebnf+diagram
syntax::= RECOVER TABLESPACE ((TBS_NAME) {"," (TBS_NAME)}) [DECRYPTION password] [MAXSIZE integer]
```

The RECOVER operation is only applicable to Standalone Deployment.

Executing the RECOVER operation requires the database to be in MOUNT or OPEN state, and the target tablespace must be in OFFLINE state, with the data file under that tablespace in RECOVER state.

The RECOVER operation cannot run in parallel with backup operations.

During the execution of RECOVER, it will automatically search for available archive files, including the default archive folder of the database and archive backup sets. If no qualifying archives are found, the RECOVER operation will fail.

### TABLESPACE

Specify the tablespace on which to perform the RECOVER operation, allowing the specified target tablespace to be restored to a state consistent with its associated database.

TBS_NAME is the name of the tablespace to be recovered, and multiple names can be specified.

### DECRYPTION

Specify the password required for recovering the archives. If the archive backup files necessary for RECOVER are encrypted, this password must be provided to recover the target archive files and apply the RECOVER to the target tablespace.

### MAXSIZE

Specify the maximum limit size for recovery of archives, with a default value of infinite, and a range of [128M, 32T].

***Example*** for Standalone Deployment
```shell
$ yasrman sys/********@192.168.1.2:1688 -c "recover tablespace spc_test maxsize 1280M " -D /home/yashan/catalog

```
