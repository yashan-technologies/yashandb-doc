## General Description

CREATE SPFILE is used to create a binary server parameter file (yasdb.spfile) for the current instance/node. The server parameter files of each instance/node are independent of each other.

The prerequisites for creating a server parameter file are as follows:

- The current instance/node has been started to the NOMOUNT stage.

- The user executing this statement must be the sys user or another user with SYSDBA, SYSOPER, or SYSBACKUP roles.

- The current instance already has a parameter file (yasdb.ini), and there is no server parameter file (yasdb.spfile) in use. Otherwise, an error will be prompted when executing this statement.

## Statement Definition

**create spfile::=**

```ebnf
= CREATE SPFILE FROM PFILE.
```

### FROM PFILE

Generate a server parameter file (yasdb.spfile) based on the currently used parameter file (yasdb.ini). The server parameter file will be stored in the $YASDB_DATA/config path.

After the server parameter file is created, the yasdb.spfile will be used for parameter initialization the next time this database instance is started. The yasdb.spfile will be given priority, and only if the verification fails will the yasdb.ini be used.