## General Description

DROP DATABASE is used to delete the current database and remove the following persistent files contained within the database:

- Data files
- Slice files
- Redo log files
- Archive log file(s) (retained by default, can be deleted together by specifying the INCLUDING ARCHIVELOG keyword)  
- Control files
- Double write file (no such file in YAC/Distributed Cluster)  
- Flashback log files


> **Note**: 
>
> This statement can only be executed by the SYS user when the database is in NOMOUNT state, and it requires the control file of the database to be complete (all control file group members must exist and be correct).
>
> In YAC/Distributed Cluster Deployment mode, this statement must be executed with the execution node role as MASTER ROLE and in NOMOUNT state.
> 
> After executing the DROP DATABASE statement, the database will automatically restart.

This statement is not applicable for ISC Distributed Cluster Deployment.

## Statement Definition

**drop database::=**

```ebnf
= DROP DATABASE [INCLUDING ARCHIVELOG].
```

### INCLUDING ARCHIVELOG

This statement determines whether to delete the database's archive log files, which defaults to not deleting them.

***Example*** for Standalone/YAC/Distributed Cluster Deployment

```sql
# Start the instance to NOMOUNT
$ yasboot cluster restart -c yashandb -m nomount

# Log in to the database as sys user
$ yasql / as sysdba

-- Drop the database
SQL> DROP DATABASE;
```
